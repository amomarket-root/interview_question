   # Complete React Native Healthcare App Documentation

## Table of Contents
1. [Project Overview](#project-overview)
2. [Project Structure](#project-structure)
3. [Installation & Setup Guide](#installation--setup-guide)
4. [Complete Source Code](#complete-source-code)
5. [Network Configuration Guide](#network-configuration-guide)
6. [API Integration](#api-integration)
7. [Debugging & Troubleshooting](#debugging--troubleshooting)
8. [Deployment Instructions](#deployment-instructions)

---

## Project Overview

### Application Details
- **Name**: Visit Care Healthcare Mobile App
- **Framework**: React Native with Expo
- **Backend**: Laravel API (existing)
- **Target Users**: Healthcare Providers, Doctors, Patients, Administrators
- **Platform**: Android & iOS

### Core Features
- Multi-user authentication system
- Healthcare provider dashboard with analytics
- Appointment management system
- User profile management
- Real-time data synchronization
- Secure token-based authentication

---

### Key Solutions Implemented

- **Network Configuration**: Updated API base URL for different environments
- **Navigation Structure**: Fixed nested navigation with proper screen names
- **Error Handling**: Comprehensive error logging and user feedback
- **Authentication**: Secure token management with AsyncStorage

---

## Project Structure

```
VisitCareHealthcare/
├── src/
│   ├── components/
│   ├── constants/
│   │   │   └── theme.ts
│   ├── contexts/
│   │   │   └── ThemeContext.tsx
│   ├── hooks/
│   │   │   └── useStyles.ts
│   ├── screens/
│   │   ├── auth/
│   │   │   ├── LoginScreen.tsx
│   │   │   └── RegisterScreen.tsx
│   │   ├── dashboard/
│   │   │   └── DashboardScreen.tsx
│   │   ├── appointments/
│   │   │   └── AppointmentsScreen.tsx
│   │   └── profile/
│   │       └── ProfileScreen.tsx
│   ├── navigation/
│   │   └── AppNavigator.tsx
│   ├── services/
│   │   ├── api.ts
│   │   ├── authService.ts
│   │   └── healthcareService.ts
│   ├── store/
│   └── types/
├── App.tsx
├── package.json
└── README.md
```

---

## Installation & Setup Guide

### Prerequisites
- Node.js (v16 or higher)
- npm or yarn
- Expo CLI
- Android Studio (for Android development)
- Laravel backend server

### Step-by-Step Installation

1. **Create Project**
   ```bash
   npx create-expo-app VisitCareHealthcare --template blank-typescript
   cd VisitCareHealthcare
   ```

2. **Install Dependencies**
   ```bash
   # Navigation
   npm install @react-navigation/native @react-navigation/stack @react-navigation/bottom-tabs
   npx expo install react-native-screens react-native-safe-area-context
   
   # UI Components
   npm install react-native-paper react-native-vector-icons
   
   # HTTP & Storage
   npm install axios @react-native-async-storage/async-storage
   
   # Form Handling
   npm install react-hook-form
   
   # Utilities
   npm install date-fns
   ```

3. **Install Expo CLI**
   ```bash
   npm install -g @expo/cli
   ```

---

## Complete Source Code

### 0. package.json

```json
{
  "name": "visitcarehealthcare",
  "version": "1.0.0",
  "main": "index.ts",
  "scripts": {
    "start": "expo start",
    "android": "expo start --android",
    "ios": "expo start --ios",
    "web": "expo start --web"
  },
  "dependencies": {
    "@expo/vector-icons": "^15.0.2",
    "@react-native-async-storage/async-storage": "^2.2.0",
    "@react-navigation/bottom-tabs": "^7.4.7",
    "@react-navigation/native": "^7.1.17",
    "@react-navigation/stack": "^7.4.8",
    "axios": "^1.12.2",
    "date-fns": "^4.1.0",
    "expo": "~54.0.9",
    "expo-status-bar": "~3.0.8",
    "react": "19.1.0",
    "react-hook-form": "^7.63.0",
    "react-native": "0.81.4",
    "react-native-paper": "^5.14.5",
    "react-native-safe-area-context": "~5.6.0",
    "react-native-screens": "~4.16.0",
    "react-native-vector-icons": "^10.3.0"
  },
  "devDependencies": {
    "@types/react": "~19.1.0",
    "@types/react-native-vector-icons": "^6.4.18",
    "typescript": "~5.9.2"
  },
  "private": true
}
```

### 1. API Configuration (`src/services/api.ts`)

```typescript
import axios, { AxiosRequestConfig, AxiosResponse } from 'axios';
import AsyncStorage from '@react-native-async-storage/async-storage';

// Environment-based configuration
const API_CONFIG = {
  baseURL: __DEV__ 
    ? 'http://10.0.2.2:8000/api'
    : 'https://your-production-domain.com/api',
  timeout: 30000,
  retryAttempts: 3,
  retryDelay: 1000,
};

// Request cache for GET requests
const requestCache = new Map<string, { data: any; timestamp: number; ttl: number }>();
const CACHE_TTL = 5 * 60 * 1000; // 5 minutes

class ApiService {
  private static instance: ApiService;
  private client;
  private requestQueue: Map<string, Promise<any>> = new Map();

  private constructor() {
    this.client = axios.create({
      baseURL: API_CONFIG.baseURL,
      timeout: API_CONFIG.timeout,
      headers: {
        'Content-Type': 'application/json',
        'Accept': 'application/json',
      },
    });

    this.setupInterceptors();
  }

  public static getInstance(): ApiService {
    if (!ApiService.instance) {
      ApiService.instance = new ApiService();
    }
    return ApiService.instance;
  }

  private setupInterceptors() {
    // Request interceptor with token management
    this.client.interceptors.request.use(
      async (config) => {
        const token = await this.getAuthToken();
        if (token) {
          config.headers.Authorization = `Bearer ${token}`;
        }
        return config;
      },
      (error) => Promise.reject(error)
    );

    // Response interceptor with retry logic
    this.client.interceptors.response.use(
      (response) => response,
      async (error) => {
        const originalRequest = error.config;
        
        // Handle 401 errors
        if (error.response?.status === 401 && !originalRequest._retry) {
          originalRequest._retry = true;
          await AsyncStorage.multiRemove(['auth_token', 'user_data']);
          return Promise.reject(error);
        }

        // Retry logic for network errors
        if (this.shouldRetry(error) && originalRequest._retryCount < API_CONFIG.retryAttempts) {
          originalRequest._retryCount = (originalRequest._retryCount || 0) + 1;
          await this.delay(API_CONFIG.retryDelay * originalRequest._retryCount);
          return this.client(originalRequest);
        }

        return Promise.reject(error);
      }
    );
  }

  private shouldRetry(error: any): boolean {
    return !error.response || error.response.status >= 500 || error.code === 'NETWORK_ERROR';
  }

  private delay(ms: number): Promise<void> {
    return new Promise(resolve => setTimeout(resolve, ms));
  }

  private async getAuthToken(): Promise<string | null> {
    try {
      return await AsyncStorage.getItem('auth_token');
    } catch {
      return null;
    }
  }

  private getCacheKey(method: string, url: string, params?: any): string {
    return `${method}:${url}:${JSON.stringify(params || {})}`;
  }

  private getFromCache(key: string): any | null {
    const cached = requestCache.get(key);
    if (cached && Date.now() - cached.timestamp < cached.ttl) {
      return cached.data;
    }
    requestCache.delete(key);
    return null;
  }

  private setCache(key: string, data: any, ttl: number = CACHE_TTL): void {
    requestCache.set(key, { data, timestamp: Date.now(), ttl });
  }

  // Optimized GET method with caching and request deduplication
  async get<T = any>(url: string, config?: AxiosRequestConfig): Promise<AxiosResponse<T>> {
    const cacheKey = this.getCacheKey('GET', url, config?.params);
    
    // Check cache first
    const cachedData = this.getFromCache(cacheKey);
    if (cachedData) {
      return { data: cachedData } as AxiosResponse<T>;
    }

    // Check if request is already in progress
    if (this.requestQueue.has(cacheKey)) {
      return this.requestQueue.get(cacheKey);
    }

    // Make new request
    const request = this.client.get<T>(url, config).then(response => {
      this.setCache(cacheKey, response.data);
      this.requestQueue.delete(cacheKey);
      return response;
    }).catch(error => {
      this.requestQueue.delete(cacheKey);
      throw error;
    });

    this.requestQueue.set(cacheKey, request);
    return request;
  }

  async post<T = any>(url: string, data?: any, config?: AxiosRequestConfig): Promise<AxiosResponse<T>> {
    return this.client.post<T>(url, data, config);
  }

  async put<T = any>(url: string, data?: any, config?: AxiosRequestConfig): Promise<AxiosResponse<T>> {
    return this.client.put<T>(url, data, config);
  }

  async delete<T = any>(url: string, config?: AxiosRequestConfig): Promise<AxiosResponse<T>> {
    return this.client.delete<T>(url, config);
  }

  // Clear cache method
  clearCache(): void {
    requestCache.clear();
  }

  // Cancel all pending requests
  cancelAllRequests(): void {
    this.requestQueue.clear();
  }
}

export default ApiService.getInstance();
```

### 2. Authentication Service (`src/services/authService.ts`)

```typescript
import ApiService from './api';
import AsyncStorage from '@react-native-async-storage/async-storage';

export interface LoginCredentials {
  email: string;
  password: string;
}

export interface RegisterData {
  name: string;
  email: string;
  password: string;
  password_confirmation: string;
  user_type: 'patient' | 'doctor' | 'healthcare' | 'admin';
  phone?: string;
}

export interface User {
  id: number;
  name: string;
  email: string;
  user_type: string;
  phone?: string;
  address?: string;
  email_verified_at?: string;
  created_at: string;
  updated_at: string;
}

class AuthService {
  private static instance: AuthService;
  private currentUser: User | null = null;
  private authStateListeners: ((isAuthenticated: boolean) => void)[] = [];

  private constructor() {
    this.initializeAuth();
  }

  public static getInstance(): AuthService {
    if (!AuthService.instance) {
      AuthService.instance = new AuthService();
    }
    return AuthService.instance;
  }

  private async initializeAuth(): Promise<void> {
    try {
      const token = await AsyncStorage.getItem('auth_token');
      if (token) {
        const userData = await AsyncStorage.getItem('user_data');
        this.currentUser = userData ? JSON.parse(userData) : null;
      }
    } catch (error) {
      console.error('Auth initialization error:', error);
    }
  }

  // Subscribe to auth state changes
  onAuthStateChange(listener: (isAuthenticated: boolean) => void): () => void {
    this.authStateListeners.push(listener);
    return () => {
      this.authStateListeners = this.authStateListeners.filter(l => l !== listener);
    };
  }

  private notifyAuthStateChange(isAuthenticated: boolean): void {
    this.authStateListeners.forEach(listener => listener(isAuthenticated));
  }

  async login(credentials: LoginCredentials): Promise<{ token: string; user: User }> {
    try {
      console.log('Attempting login with:', { email: credentials.email });
      
      const response = await ApiService.post('/login', credentials);
      const { token, user } = response.data;
      
      if (!token || !user) {
        throw new Error('Invalid response format from server');
      }
      
      // Store auth data
      await Promise.all([
        AsyncStorage.setItem('auth_token', token),
        AsyncStorage.setItem('user_data', JSON.stringify(user))
      ]);
      
      this.currentUser = user;
      this.notifyAuthStateChange(true);
      
      console.log('Auth data saved successfully');
      return response.data;
    } catch (error: any) {
      console.error('Login error:', error);
      this.handleAuthError(error);
    }
  }

  async register(data: RegisterData): Promise<{ token: string; user: User }> {
    try {
      console.log('Attempting registration with:', { 
        email: data.email, 
        user_type: data.user_type 
      });
      
      const response = await ApiService.post('/register', data);
      const { token, user } = response.data;
      
      if (!token || !user) {
        throw new Error('Invalid response format from server');
      }
      
      // Store auth data
      await Promise.all([
        AsyncStorage.setItem('auth_token', token),
        AsyncStorage.setItem('user_data', JSON.stringify(user))
      ]);
      
      this.currentUser = user;
      this.notifyAuthStateChange(true);
      
      return response.data;
    } catch (error: any) {
      console.error('Registration error:', error);
      this.handleAuthError(error);
    }
  }

  async logout(): Promise<void> {
    try {
      console.log('Attempting logout');
      
      // Try to logout from server (don't block on failure)
      try {
        await ApiService.post('/logout');
      } catch (error) {
        console.warn('Server logout failed:', error);
      }
      
      // Clear local storage
      await Promise.all([
        AsyncStorage.multiRemove(['auth_token', 'user_data']),
        ApiService.clearCache(),
        ApiService.cancelAllRequests()
      ]);
      
      this.currentUser = null;
      this.notifyAuthStateChange(false);
      
      console.log('Auth data cleared');
    } catch (error) {
      console.error('Logout error:', error);
      throw error;
    }
  }

  getCurrentUser(): User | null {
    return this.currentUser;
  }

  async refreshUserData(): Promise<User | null> {
    try {
      const userData = await AsyncStorage.getItem('user_data');
      this.currentUser = userData ? JSON.parse(userData) : null;
      return this.currentUser;
    } catch (error) {
      console.error('Error refreshing user data:', error);
      return null;
    }
  }

  async isAuthenticated(): Promise<boolean> {
    try {
      const token = await AsyncStorage.getItem('auth_token');
      return !!token;
    } catch (error) {
      console.error('Error checking auth state:', error);
      return false;
    }
  }

  async getAuthToken(): Promise<string | null> {
    try {
      return await AsyncStorage.getItem('auth_token');
    } catch (error) {
      console.error('Error getting auth token:', error);
      return null;
    }
  }

  private handleAuthError(error: any): never {
    if (error.response) {
      const errorMessage = error.response.data?.message || 
                         error.response.data?.error || 
                         'Authentication failed';
      throw new Error(errorMessage);
    } else if (error.request) {
      throw new Error('Network error - please check your connection');
    } else {
      throw new Error(error.message || 'Unknown error occurred');
    }
  }
}

export default AuthService.getInstance();
```

### 3. Healthcare Service (`src/services/healthcareService.ts`)

```typescript
import ApiService from './api';

export interface DashboardStats {
  total_facilities?: number;
  connected_doctors?: number;
  today_appointments?: number;
  monthly_appointments?: number;
  pending_requests?: number;
  total_patients?: number;
}

export interface Appointment {
  id: number;
  patient_name: string;
  doctor_name: string;
  appointment_date: string;
  appointment_time: string;
  status: string;
  type: string;
  notes?: string;
}

export interface AppointmentsResponse {
  appointments: Appointment[];
  pagination?: {
    current_page: number;
    total_pages: number;
    total_count: number;
  };
}

class HealthcareService {
  private static instance: HealthcareService;

  private constructor() { }

  public static getInstance(): HealthcareService {
    if (!HealthcareService.instance) {
      HealthcareService.instance = new HealthcareService();
    }
    return HealthcareService.instance;
  }

  async getDashboardStats(): Promise<DashboardStats> {
    try {
      const response = await ApiService.get('/healthcare/dashboard-stats');
      return response.data;
    } catch (error) {
      console.error('Failed to fetch dashboard stats:', error);
      this.handleServiceError(error, 'Failed to load dashboard statistics');
    }
  }

  async getProfile(): Promise<any> {
    try {
      const response = await ApiService.get('/healthcare/profile');
      return response.data;
    } catch (error) {
      console.error('Failed to fetch profile:', error);
      this.handleServiceError(error, 'Failed to load profile');
    }
  }

  async updateProfile(data: FormData): Promise<any> {
    try {
      const response = await ApiService.post('/healthcare/profile', data, {
        headers: { 'Content-Type': 'multipart/form-data' }
      });
      return response.data;
    } catch (error) {
      console.error('Failed to update profile:', error);
      this.handleServiceError(error, 'Failed to update profile');
    }
  }

  async getAppointments(
    page: number = 1,
    filters: Record<string, any> = {}
  ): Promise<AppointmentsResponse> {
    try {
      const params = new URLSearchParams();
      params.append('page', page.toString());
      Object.entries(filters).forEach(([key, value]) => {
        if (value !== undefined && value !== null) {
          params.append(key, String(value));
        }
      });

      // API call
      const response = await ApiService.get(`healthcare/appointments?${params.toString()}`);

      // Remap backend data structure to fit your Appointment[] type:
      const raw = response.data;
      const appointments = (raw.data || []).map((a: any) => ({
        id: a.id,
        appointment_number: a.appointment_number,
        appointment_date: a.appointment_date,
        appointment_time: a.appointment_time,
        status: a.status,
        amount: a.amount,
        notes: a.notes,
        type: a.symptoms || a.type || '',
        patient_name: a.patient?.name,
        doctor_name: a.doctor?.user?.name,
        patient: a.patient,
        doctor: a.doctor,
        clinic: a.clinic,
      }));
      return {
        appointments,
        pagination: {
          current_page: raw.current_page || 1,
          total_pages: Math.ceil((raw.total || 1) / (raw.per_page || 1)),
          total_count: raw.total || appointments.length,
        },
      };
    } catch (error) {
      console.error('Failed to fetch appointments', error);
      throw error;
    }
  }

  async updateAppointmentStatus(appointmentId: number, status: string, notes?: string): Promise<any> {
    try {
      const response = await ApiService.post(`healthcare/appointments/${appointmentId}/update-status`, {
        status,
        notes
      });
      return response.data;
    } catch (error) {
      console.error('Failed to update appointment status:', error);
      this.handleServiceError(error, 'Failed to update appointment status');
    }
  }

  async getConnectionRequests(): Promise<any> {
    try {
      const response = await ApiService.get('/healthcare/connection-requests');
      return response.data;
    } catch (error) {
      console.error('Failed to fetch connection requests:', error);
      this.handleServiceError(error, 'Failed to load connection requests');
    }
  }

  async respondToConnectionRequest(requestId: number, status: string, data: Record<string, any> = {}): Promise<any> {
    try {
      const response = await ApiService.post(`/healthcare/connection-requests/${requestId}/respond`, {
        status,
        ...data
      });
      return response.data;
    } catch (error) {
      console.error('Failed to respond to connection request:', error);
      this.handleServiceError(error, 'Failed to respond to connection request');
    }
  }

  async getConnectedDoctors(): Promise<any> {
    try {
      const response = await ApiService.get('/healthcare/connected-doctors');
      return response.data;
    } catch (error) {
      console.error('Failed to fetch connected doctors:', error);
      this.handleServiceError(error, 'Failed to load connected doctors');
    }
  }

  private handleServiceError(error: any, defaultMessage: string): never {
    if (error.response?.status === 401) {
      throw new Error('Session expired. Please log in again.');
    }

    const message = error.response?.data?.message || defaultMessage;
    throw new Error(message);
  }
}

export default HealthcareService.getInstance();
```

### 4. Login Screen (`src/screens/auth/LoginScreen.tsx`)

```typescript
import React, { useState } from 'react';
import { View, ScrollView, KeyboardAvoidingView, Platform, Image } from 'react-native';
import { TextInput, Button, Card, Title, Text, HelperText, IconButton } from 'react-native-paper';
import { useForm, Controller } from 'react-hook-form';
import Icon from 'react-native-vector-icons/MaterialCommunityIcons';
import { useTheme } from '../../contexts/ThemeContext';
import { useStyles } from '../../hooks/useStyles';
import authService, { LoginCredentials } from '../../services/authService';
import { Alert } from 'react-native';

interface LoginScreenProps {
  navigation: any;
}

export default function LoginScreen({ navigation }: LoginScreenProps) {
  const [loading, setLoading] = useState(false);
  const [showPassword, setShowPassword] = useState(false);
  const [imageError, setImageError] = useState(false);
  const { toggleTheme, isDark } = useTheme();
  
  const styles = useStyles((theme) => ({
    container: {
      flex: 1,
      backgroundColor: theme.colors.background,
    },
    scrollContent: {
      flexGrow: 1,
      justifyContent: 'center',
    },
    content: {
      flex: 1,
      padding: theme.spacing.md,
      justifyContent: 'center',
    },
    card: {
      padding: theme.spacing.md,
      ...theme.shadows.medium,
      borderRadius: theme.borderRadius.md,
      backgroundColor: theme.colors.surface,
    },
    iconContainer: {
      alignItems: 'center',
      marginBottom: theme.spacing.md,
      flexDirection: 'row',
      justifyContent: 'space-between',
    },
    logoContainer: {
      flex: 1,
      alignItems: 'center',
    },
    themeToggle: {
      margin: 0,
    },
    logoImage: {
      width: 100,
      height: 80,
      marginBottom: theme.spacing.xs,
    },
    title: {
      textAlign: 'center',
      marginBottom: theme.spacing.xs,
      color: theme.colors.primary,
      fontSize: theme.typography.sizes.xl,
      fontWeight: theme.typography.weights.bold,
      fontFamily: theme.typography.fontFamily,
    },
    subtitle: {
      textAlign: 'center',
      marginBottom: theme.spacing.xxl,
      color: theme.colors.textSecondary,
      fontSize: theme.typography.sizes.md,
      fontFamily: theme.typography.fontFamily,
    },
    input: {
      marginBottom: theme.spacing.xs,
      backgroundColor: theme.colors.surface,
    },
    button: {
      marginTop: theme.spacing.lg,
      marginBottom: theme.spacing.md,
      borderRadius: theme.borderRadius.sm,
    },
    buttonContent: {
      paddingVertical: theme.spacing.xs,
    },
    linkButton: {
      marginTop: theme.spacing.xs,
    },
    footer: {
      alignItems: 'center',
      marginTop: theme.spacing.xxl,
      paddingTop: theme.spacing.md,
    },
    footerText: {
      fontSize: theme.typography.sizes.sm,
      color: theme.colors.textSecondary,
      marginBottom: theme.spacing.xs,
      fontFamily: theme.typography.fontFamily,
    },
    securityIcons: {
      flexDirection: 'row',
      alignItems: 'center',
      justifyContent: 'center',
    },
    iconButton: {
      margin: 0,
      marginHorizontal: 4,
    },
  }));
  
  const { control, handleSubmit, formState: { errors } } = useForm<LoginCredentials>({
    defaultValues: {
      email: '',
      password: ''
    }
  });

  const onLogin = async (data: LoginCredentials) => {
    console.log('Login form submitted with:', { email: data.email });
    setLoading(true);
    
    try {
      const response = await authService.login(data);
      console.log('Login successful:', response);
      
      Alert.alert(
        'Login Successful', 
        `Welcome ${response.user.name}!`,
        [{ 
          text: 'OK', 
          onPress: () => {
            console.log('Login completed, navigation will update automatically');
          }
        }]
      );
    } catch (error: any) {
      console.error('Login failed:', error);
      
      Alert.alert(
        'Login Failed', 
        error.message || 'Invalid credentials. Please try again.',
        [{ text: 'OK' }]
      );
    } finally {
      setLoading(false);
    }
  };

  return (
    <KeyboardAvoidingView 
      style={styles.container} 
      behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
    >
      <ScrollView contentContainerStyle={styles.scrollContent}>
        <View style={styles.content}>
          <Card style={styles.card}>
            <Card.Content>
              <View style={styles.iconContainer}>
                <View style={styles.logoContainer}>
                  {!imageError ? (
                    <Image 
                      source={require('../../../assets/images/icon.webp')} 
                      style={styles.logoImage}
                      resizeMode="contain"
                      onError={() => setImageError(true)}
                    />
                  ) : (
                    <Icon name="hospital-box" size={80} color="#2E7D32" />
                  )}
                </View>
                <IconButton
                  icon={isDark ? 'weather-sunny' : 'weather-night'}
                  iconColor={isDark ? '#FFA726' : '#2196F3'}
                  size={24}
                  onPress={toggleTheme}
                  style={styles.themeToggle}
                />
              </View>
              
              <Title style={styles.title}>Visit Care Healthcare</Title>
              <Text style={styles.subtitle}>Healthcare Management System</Text>
              
              <Controller
                control={control}
                name="email"
                rules={{ 
                  required: 'Email is required',
                  pattern: {
                    value: /^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$/i,
                    message: 'Invalid email address'
                  }
                }}
                render={({ field: { onChange, value } }) => (
                  <>
                    <TextInput
                      label="Email Address"
                      value={value}
                      onChangeText={onChange}
                      mode="outlined"
                      style={styles.input}
                      keyboardType="email-address"
                      autoCapitalize="none"
                      autoCorrect={false}
                      error={!!errors.email}
                      disabled={loading}
                      left={<TextInput.Icon icon="email" />}
                    />
                    {errors.email && (
                      <HelperText type="error" visible={!!errors.email}>
                        {errors.email.message}
                      </HelperText>
                    )}
                  </>
                )}
              />
              
              <Controller
                control={control}
                name="password"
                rules={{ 
                  required: 'Password is required',
                  minLength: {
                    value: 6,
                    message: 'Password must be at least 6 characters'
                  }
                }}
                render={({ field: { onChange, value } }) => (
                  <>
                    <TextInput
                      label="Password"
                      value={value}
                      onChangeText={onChange}
                      mode="outlined"
                      secureTextEntry={!showPassword}
                      style={styles.input}
                      error={!!errors.password}
                      disabled={loading}
                      left={<TextInput.Icon icon="lock" />}
                      right={
                        <TextInput.Icon 
                          icon={showPassword ? "eye-off" : "eye"} 
                          onPress={() => setShowPassword(!showPassword)}
                        />
                      }
                    />
                    {errors.password && (
                      <HelperText type="error" visible={!!errors.password}>
                        {errors.password.message}
                      </HelperText>
                    )}
                  </>
                )}
              />
              
              <Button
                mode="contained"
                onPress={handleSubmit(onLogin)}
                loading={loading}
                disabled={loading}
                style={styles.button}
                contentStyle={styles.buttonContent}
                icon="login"
              >
                {loading ? 'Logging in...' : 'Login'}
              </Button>
              
              <Button
                mode="text"
                onPress={() => navigation.navigate('Register')}
                disabled={loading}
                style={styles.linkButton}
                icon="account-plus"
              >
                Don't have an account? Register
              </Button>

              <Button
                mode="text"
                onPress={() => Alert.alert('Info', 'Forgot password functionality will be implemented')}
                disabled={loading}
                style={styles.linkButton}
                icon="help-circle"
              >
                Forgot Password?
              </Button>
            </Card.Content>
          </Card>

          <View style={styles.footer}>
            <Text style={styles.footerText}>
              Secure healthcare management platform
            </Text>
            <View style={styles.securityIcons}>
              <IconButton 
                icon="shield-check" 
                iconColor="#4CAF50" 
                size={20}
                style={styles.iconButton}
              />
              <IconButton 
                icon="lock" 
                iconColor="#4CAF50" 
                size={20}
                style={styles.iconButton}
              />
              <IconButton 
                icon="hospital" 
                iconColor="#4CAF50" 
                size={20}
                style={styles.iconButton}
              />
            </View>
          </View>
        </View>
      </ScrollView>
    </KeyboardAvoidingView>
  );
}
```
### 5. Register Screen (`src/screens/auth/RegisterScreen.tsx`)

```typescript
import React, { useState } from 'react';
import { View, ScrollView, KeyboardAvoidingView, Platform, Image, Alert } from 'react-native';
import { TextInput, Button, Card, Title, Text, HelperText, RadioButton, IconButton } from 'react-native-paper';
import { useForm, Controller } from 'react-hook-form';
import { MaterialCommunityIcons } from '@expo/vector-icons';
import { useTheme } from '../../contexts/ThemeContext';
import { useStyles } from '../../hooks/useStyles';
import authService, { RegisterData } from '../../services/authService';

interface RegisterScreenProps {
  navigation: any;
}

export default function RegisterScreen({ navigation }: RegisterScreenProps) {
  const [loading, setLoading] = useState(false);
  const [showPassword, setShowPassword] = useState(false);
  const [showConfirmPassword, setShowConfirmPassword] = useState(false);
  const [imageError, setImageError] = useState(false);
  const { toggleTheme, isDark } = useTheme();
  
  const styles = useStyles((theme) => ({
    container: {
      flex: 1,
      backgroundColor: theme.colors.background,
    },
    scrollContent: {
      flexGrow: 1,
      paddingVertical: theme.spacing.lg,
    },
    content: {
      flex: 1,
      padding: theme.spacing.md,
      justifyContent: 'center',
    },
    card: {
      padding: theme.spacing.md,
      ...theme.shadows.medium,
      borderRadius: theme.borderRadius.md,
      backgroundColor: theme.colors.surface,
    },
    iconContainer: {
      alignItems: 'center',
      marginBottom: theme.spacing.md,
      flexDirection: 'row',
      justifyContent: 'space-between',
    },
    logoContainer: {
      flex: 1,
      alignItems: 'center',
    },
    themeToggle: {
      margin: 0,
    },
    logoImage: {
      width: 80,
      height: 60,
      marginBottom: theme.spacing.xs,
    },
    title: {
      textAlign: 'center',
      marginBottom: theme.spacing.xs,
      color: theme.colors.primary,
      fontSize: theme.typography.sizes.xl,
      fontWeight: theme.typography.weights.bold,
      fontFamily: theme.typography.fontFamily,
    },
    subtitle: {
      textAlign: 'center',
      marginBottom: theme.spacing.xxl,
      color: theme.colors.textSecondary,
      fontSize: theme.typography.sizes.md,
      fontFamily: theme.typography.fontFamily,
    },
    input: {
      marginBottom: theme.spacing.xs,
      backgroundColor: theme.colors.surface,
    },
    sectionTitle: {
      fontSize: theme.typography.sizes.md,
      fontWeight: theme.typography.weights.semibold,
      marginTop: theme.spacing.md,
      marginBottom: theme.spacing.sm,
      color: theme.colors.text,
      fontFamily: theme.typography.fontFamily,
    },
    radioGroup: {
      marginBottom: theme.spacing.md,
    },
    radioItem: {
      flexDirection: 'row',
      alignItems: 'center',
      marginBottom: theme.spacing.xs,
    },
    radioLabel: {
      marginLeft: theme.spacing.xs,
      fontSize: theme.typography.sizes.md,
      color: theme.colors.text,
      fontFamily: theme.typography.fontFamily,
    },
    button: {
      marginTop: theme.spacing.lg,
      marginBottom: theme.spacing.md,
      borderRadius: theme.borderRadius.sm,
    },
    buttonContent: {
      paddingVertical: theme.spacing.xs,
    },
    linkButton: {
      marginTop: theme.spacing.xs,
    },
  }));
  
  const { control, handleSubmit, formState: { errors }, watch } = useForm<RegisterData>({
    defaultValues: {
      name: '',
      email: '',
      password: '',
      password_confirmation: '',
      user_type: 'healthcare',
      phone: ''
    }
  });

  const password = watch('password');

  const onRegister = async (data: RegisterData) => {
    console.log('Registration form submitted:', { 
      email: data.email, 
      user_type: data.user_type 
    });
    setLoading(true);
    
    try {
      const response = await authService.register(data);
      console.log('Registration successful:', response);
      
      Alert.alert(
        'Registration Successful', 
        `Welcome ${response.user.name}! Your account has been created.`,
        [{ 
          text: 'OK', 
          onPress: () => {
            console.log('Registration completed, navigation will update automatically');
          }
        }]
      );
    } catch (error: any) {
      console.error('Registration failed:', error);
      
      Alert.alert(
        'Registration Failed', 
        error.message || 'Failed to create account. Please try again.',
        [{ text: 'OK' }]
      );
    } finally {
      setLoading(false);
    }
  };

  return (
    <KeyboardAvoidingView 
      style={styles.container} 
      behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
    >
      <ScrollView contentContainerStyle={styles.scrollContent}>
        <View style={styles.content}>
          <Card style={styles.card}>
            <Card.Content>
              <View style={styles.iconContainer}>
                <View style={styles.logoContainer}>
                  {!imageError ? (
                    <Image 
                      source={require('../../../assets/images/icon.webp')} 
                      style={styles.logoImage}
                      resizeMode="contain"
                      onError={() => setImageError(true)}
                    />
                  ) : (
                    <MaterialCommunityIcons name="hospital-box" size={60} color={isDark ? '#4de352' : '#10d915'} />
                  )}
                </View>
                <IconButton
                  icon={isDark ? 'weather-sunny' : 'weather-night'}
                  iconColor={isDark ? '#FFA726' : '#2196F3'}
                  size={24}
                  onPress={toggleTheme}
                  style={styles.themeToggle}
                />
              </View>
              
              <Title style={styles.title}>Create Account</Title>
              <Text style={styles.subtitle}>Join Visit Care Healthcare</Text>
              
              <Controller
                control={control}
                name="name"
                rules={{ required: 'Name is required' }}
                render={({ field: { onChange, value } }) => (
                  <>
                    <TextInput
                      label="Full Name"
                      value={value}
                      onChangeText={onChange}
                      mode="outlined"
                      style={styles.input}
                      error={!!errors.name}
                      disabled={loading}
                      left={<TextInput.Icon icon="account" />}
                    />
                    {errors.name && (
                      <HelperText type="error" visible={!!errors.name}>
                        {errors.name.message}
                      </HelperText>
                    )}
                  </>
                )}
              />
              
              <Controller
                control={control}
                name="email"
                rules={{ 
                  required: 'Email is required',
                  pattern: {
                    value: /^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$/i,
                    message: 'Invalid email address'
                  }
                }}
                render={({ field: { onChange, value } }) => (
                  <>
                    <TextInput
                      label="Email Address"
                      value={value}
                      onChangeText={onChange}
                      mode="outlined"
                      style={styles.input}
                      keyboardType="email-address"
                      autoCapitalize="none"
                      error={!!errors.email}
                      disabled={loading}
                      left={<TextInput.Icon icon="email" />}
                    />
                    {errors.email && (
                      <HelperText type="error" visible={!!errors.email}>
                        {errors.email.message}
                      </HelperText>
                    )}
                  </>
                )}
              />

              <Controller
                control={control}
                name="phone"
                render={({ field: { onChange, value } }) => (
                  <TextInput
                    label="Phone Number (Optional)"
                    value={value}
                    onChangeText={onChange}
                    mode="outlined"
                    style={styles.input}
                    keyboardType="phone-pad"
                    disabled={loading}
                    left={<TextInput.Icon icon="phone" />}
                  />
                )}
              />

              <Text style={styles.sectionTitle}>Account Type</Text>
              <Controller
                control={control}
                name="user_type"
                render={({ field: { onChange, value } }) => (
                  <View style={styles.radioGroup}>
                    <RadioButton.Group onValueChange={onChange} value={value}>
                      <View style={styles.radioItem}>
                        <RadioButton value="healthcare" />
                        <MaterialCommunityIcons 
                          name="hospital-building" 
                          size={20} 
                          color={isDark ? '#FFF' : '#666'} 
                          style={{ marginLeft: 8 }} 
                        />
                        <Text style={styles.radioLabel}>Healthcare Provider</Text>
                      </View>
                    </RadioButton.Group>
                  </View>
                )}
              />
              
              <Controller
                control={control}
                name="password"
                rules={{ 
                  required: 'Password is required',
                  minLength: {
                    value: 8,
                    message: 'Password must be at least 8 characters'
                  }
                }}
                render={({ field: { onChange, value } }) => (
                  <>
                    <TextInput
                      label="Password"
                      value={value}
                      onChangeText={onChange}
                      mode="outlined"
                      secureTextEntry={!showPassword}
                      style={styles.input}
                      error={!!errors.password}
                      disabled={loading}
                      left={<TextInput.Icon icon="lock" />}
                      right={
                        <TextInput.Icon 
                          icon={showPassword ? "eye-off" : "eye"} 
                          onPress={() => setShowPassword(!showPassword)}
                        />
                      }
                    />
                    {errors.password && (
                      <HelperText type="error" visible={!!errors.password}>
                        {errors.password.message}
                      </HelperText>
                    )}
                  </>
                )}
              />
              
              <Controller
                control={control}
                name="password_confirmation"
                rules={{ 
                  required: 'Please confirm your password',
                  validate: (value) => value === password || 'Passwords do not match'
                }}
                render={({ field: { onChange, value } }) => (
                  <>
                    <TextInput
                      label="Confirm Password"
                      value={value}
                      onChangeText={onChange}
                      mode="outlined"
                      secureTextEntry={!showConfirmPassword}
                      style={styles.input}
                      error={!!errors.password_confirmation}
                      disabled={loading}
                      left={<TextInput.Icon icon="lock-check" />}
                      right={
                        <TextInput.Icon 
                          icon={showConfirmPassword ? "eye-off" : "eye"} 
                          onPress={() => setShowConfirmPassword(!showConfirmPassword)}
                        />
                      }
                    />
                    {errors.password_confirmation && (
                      <HelperText type="error" visible={!!errors.password_confirmation}>
                        {errors.password_confirmation.message}
                      </HelperText>
                    )}
                  </>
                )}
              />
              
              <Button
                mode="contained"
                onPress={handleSubmit(onRegister)}
                loading={loading}
                disabled={loading}
                style={styles.button}
                contentStyle={styles.buttonContent}
                icon="account-plus"
              >
                {loading ? 'Creating Account...' : 'Register'}
              </Button>
              
              <Button
                mode="text"
                onPress={() => navigation.navigate('Login')}
                disabled={loading}
                style={styles.linkButton}
                icon="login"
              >
                Already have an account? Login
              </Button>
            </Card.Content>
          </Card>
        </View>
      </ScrollView>
    </KeyboardAvoidingView>
  );
}
```

### 6. Navigation Setup (`src/navigation/AppNavigator.tsx`)

```typescript
import React, { useEffect, useState, useCallback, useMemo, Suspense } from 'react';
import { 
  View, 
  Text, 
  StyleSheet, 
  ActivityIndicator, 
  Platform, 
  TouchableOpacity, 
  Modal,
  Dimensions,
  StatusBar 
} from 'react-native';
import { NavigationContainer, DefaultTheme, DarkTheme } from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack';
import { createBottomTabNavigator, BottomTabNavigationOptions } from '@react-navigation/bottom-tabs';
import { Avatar, Card, Button, Divider } from 'react-native-paper';
import { MaterialCommunityIcons } from '@expo/vector-icons';
import type { RouteProp, ParamListBase, Theme } from '@react-navigation/native';
import type { BottomTabNavigationProp } from '@react-navigation/bottom-tabs';

import { useTheme } from '../contexts/ThemeContext';
import authService from '../services/authService';

// Lazy load screens for better performance
const LoginScreen = React.lazy(() => import('../screens/auth/LoginScreen'));
const RegisterScreen = React.lazy(() => import('../screens/auth/RegisterScreen'));
const DashboardScreen = React.lazy(() => import('../screens/dashboard/DashboardScreen'));
const AppointmentsScreen = React.lazy(() => import('../screens/appointments/AppointmentsScreen'));
const ProfileScreen = React.lazy(() => import('../screens/profile/ProfileScreen'));

const Stack = createStackNavigator();
const Tab = createBottomTabNavigator();

// Type definitions for better TypeScript support
type TabParamList = {
  Dashboard: undefined;
  Appointments: undefined;
  Profile: undefined;
};

type StackParamList = {
  Login: undefined;
  Register: undefined;
  Main: undefined;
};

// Font weight type definition
type FontWeight = "100" | "200" | "300" | "400" | "500" | "600" | "700" | "800" | "900" | "bold" | "normal";

// Get screen dimensions
const { width: screenWidth, height: screenHeight } = Dimensions.get('window');

// Avatar Dropdown Component with Modal
const AvatarDropdown = React.memo(({ currentUser, onLogout, navigation }: {
  currentUser: any;
  onLogout: () => void;
  navigation: any;
}) => {
  const [isVisible, setIsVisible] = useState(false);
  const { theme } = useTheme();

  const openDropdown = useCallback(() => {
    setIsVisible(true);
  }, []);

  const closeDropdown = useCallback(() => {
    setIsVisible(false);
  }, []);

  const handleProfilePress = useCallback(() => {
    closeDropdown();
    setTimeout(() => {
      navigation.navigate('Profile');
    }, 100);
  }, [navigation, closeDropdown]);

  const handleSettingsPress = useCallback(() => {
    closeDropdown();
    setTimeout(() => {
      console.log('Settings pressed - Navigate to settings screen');
      // Add navigation to settings screen when available
    }, 100);
  }, [closeDropdown]);

  const handleLogoutPress = useCallback(async () => {
    closeDropdown();
    setTimeout(async () => {
      try {
        await onLogout();
      } catch (error) {
        console.error('Logout error:', error);
      }
    }, 100);
  }, [onLogout, closeDropdown]);

  // Get user initials for avatar
  const getUserInitials = useCallback((name: string) => {
    if (!name) return 'U';
    const words = name.trim().split(' ');
    if (words.length === 1) {
      return words[0].charAt(0).toUpperCase();
    }
    return (words[0].charAt(0) + words[words.length - 1].charAt(0)).toUpperCase();
  }, []);

  // Get avatar background color based on user type
  const getAvatarColor = useCallback((userType: string) => {
    const colors = {
      doctor: '#4CAF50',
      healthcare: '#2196F3',
      patient: '#FF9800',
      admin: '#9C27B0',
      default: theme.colors.primary
    };
    return colors[userType?.toLowerCase() as keyof typeof colors] || colors.default;
  }, [theme.colors.primary]);

  const avatarColor = getAvatarColor(currentUser?.user_type || '');
  const userInitials = getUserInitials(currentUser?.name || 'User');

  // Menu items configuration
  const menuItems = [
    {
      id: 'profile',
      title: 'Profile',
      icon: 'account',
      onPress: handleProfilePress,
      color: theme.colors.text,
    },
    {
      id: 'settings',
      title: 'Settings',
      icon: 'cog',
      onPress: handleSettingsPress,
      color: theme.colors.text,
    },
    {
      id: 'divider',
      isDivider: true,
    },
    {
      id: 'logout',
      title: 'Logout',
      icon: 'logout',
      onPress: handleLogoutPress,
      color: '#F44336',
    },
  ];

  return (
    <View style={styles.avatarContainer}>
      {/* Avatar Button */}
      <TouchableOpacity 
        onPress={openDropdown}
        style={styles.avatarButton}
        activeOpacity={0.8}
        accessible
        accessibilityLabel="User menu"
        accessibilityHint="Opens user menu with profile and settings options"
      >
        <Avatar.Text
          size={36}
          label={userInitials}
          style={[styles.avatar, { backgroundColor: avatarColor }]}
          labelStyle={styles.avatarLabel}
        />
      </TouchableOpacity>

      {/* Modal Dropdown */}
      <Modal
        visible={isVisible}
        transparent={true}
        animationType="fade"
        onRequestClose={closeDropdown}
        statusBarTranslucent={true}
      >
        {/* Backdrop */}
        <TouchableOpacity 
          style={styles.backdrop}
          activeOpacity={1}
          onPress={closeDropdown}
        >
          <View style={styles.modalContainer}>
            {/* Dropdown Card */}
            <Card style={[styles.dropdownCard, { backgroundColor: theme.colors.surface }]}>
              {/* User Info Header */}
              <View style={styles.userInfoHeader}>
                <Avatar.Text
                  size={50}
                  label={userInitials}
                  style={[styles.headerAvatar, { backgroundColor: avatarColor }]}
                  labelStyle={styles.headerAvatarLabel}
                />
                <View style={styles.userDetails}>
                  <Text style={[styles.userName, { color: theme.colors.text }]} numberOfLines={1}>
                    {currentUser?.name || 'User'}
                  </Text>
                  <Text style={[styles.userEmail, { color: theme.colors.textSecondary }]} numberOfLines={1}>
                    {currentUser?.email || 'user@example.com'}
                  </Text>
                  <Text style={[styles.userType, { color: theme.colors.primary }]}>
                    {(currentUser?.user_type || 'user').charAt(0).toUpperCase() + 
                     (currentUser?.user_type || 'user').slice(1)}
                  </Text>
                </View>
              </View>

              {/* Menu Items */}
              <View style={styles.menuItemsContainer}>
                {menuItems.map((item) => {
                  if (item.isDivider) {
                    return (
                      <Divider 
                        key={item.id}
                        style={[styles.menuDivider, { backgroundColor: theme.colors.border || '#e0e0e0' }]} 
                      />
                    );
                  }

                  return (
                    <TouchableOpacity
                      key={item.id}
                      style={[styles.menuItem, { borderColor: theme.colors.border || '#e0e0e0' }]}
                      onPress={item.onPress}
                      activeOpacity={0.7}
                      accessible
                      accessibilityLabel={item.title}
                    >
                      <MaterialCommunityIcons 
                        name={item.icon as any} 
                        size={22} 
                        color={item.color} 
                        style={styles.menuIcon}
                      />
                      <Text style={[styles.menuItemText, { color: item.color }]}>
                        {item.title}
                      </Text>
                    </TouchableOpacity>
                  );
                })}
              </View>
            </Card>
          </View>
        </TouchableOpacity>
      </Modal>
    </View>
  );
});

// Loading component with better UX
const LoadingScreen = React.memo(() => {
  const { theme } = useTheme();
  
  return (
    <View style={[styles.loadingContainer, { backgroundColor: theme.colors.background }]}>
      <ActivityIndicator size="large" color={theme.colors.primary} />
      <Text style={[styles.loadingText, { color: theme.colors.text }]}>Loading...</Text>
    </View>
  );
});

// Suspense fallback component
const SuspenseFallback = React.memo(() => {
  const { theme } = useTheme();
  
  return (
    <View style={[styles.loadingContainer, { backgroundColor: theme.colors.background }]}>
      <ActivityIndicator size="large" color={theme.colors.primary} />
      <Text style={[styles.loadingText, { color: theme.colors.text }]}>Loading Screen...</Text>
    </View>
  );
});

// Optimized main tabs with memoization and proper TypeScript
const MainTabs = React.memo(() => {
  const [currentUser, setCurrentUser] = useState<any>(null);
  const { theme, isDark } = useTheme();

  useEffect(() => {
    const loadCurrentUser = () => {
      const user = authService.getCurrentUser();
      setCurrentUser(user);
    };
    
    loadCurrentUser();
    
    // Subscribe to auth state changes
    const unsubscribe = authService.onAuthStateChange(() => {
      loadCurrentUser();
    });

    return unsubscribe;
  }, []);

  const handleLogout = useCallback(async () => {
    try {
      await authService.logout();
    } catch (error) {
      console.error('Logout error:', error);
    }
  }, []);

  // Fixed screenOptions with proper generic types
  const screenOptions = useCallback((props: {
    route: RouteProp<ParamListBase, string>;
    navigation: BottomTabNavigationProp<ParamListBase, string, undefined>;
    theme: Theme;
  }): BottomTabNavigationOptions => {
    const { route, navigation } = props;
    
    return {
      tabBarIcon: ({ focused, color, size }) => {
        let iconName: keyof typeof MaterialCommunityIcons.glyphMap;
        
        switch (route.name) {
          case 'Dashboard':
            iconName = focused ? 'view-dashboard' : 'view-dashboard-variant';
            break;
          case 'Appointments':
            iconName = focused ? 'calendar-clock' : 'calendar-outline';
            break;
          case 'Profile':
            iconName = focused ? 'account' : 'account-outline';
            break;
          default:
            iconName = 'circle';
            break;
        }
        
        return <MaterialCommunityIcons name={iconName} size={size} color={color} />;
      },
      tabBarActiveTintColor: theme.colors.primary,
      tabBarInactiveTintColor: theme.colors.textSecondary,
      tabBarStyle: { 
        backgroundColor: theme.colors.surface,
        borderTopColor: theme.colors.border || '#e0e0e0',
        elevation: Platform.OS === 'android' ? 8 : 0,
        shadowOffset: Platform.OS === 'ios' ? {
          width: 0,
          height: -3,
        } : { width: 0, height: 0 },
        shadowRadius: Platform.OS === 'ios' ? 3 : 0,
        shadowOpacity: Platform.OS === 'ios' ? 0.1 : 0,
        shadowColor: Platform.OS === 'ios' ? '#000' : 'transparent',
      },
      headerStyle: { 
        backgroundColor: theme.colors.surface,
        elevation: Platform.OS === 'android' ? 4 : 0,
        shadowOffset: Platform.OS === 'ios' ? {
          width: 0,
          height: 2,
        } : { width: 0, height: 0 },
        shadowRadius: Platform.OS === 'ios' ? 3.84 : 0,
        shadowOpacity: Platform.OS === 'ios' ? 0.25 : 0,
        shadowColor: Platform.OS === 'ios' ? '#000' : 'transparent',
      },
      headerTintColor: theme.colors.text,
      tabBarLabelStyle: {
        fontSize: 12,
        fontWeight: '600',
      },
      tabBarIconStyle: {
        marginBottom: -3,
      },
      // Add avatar dropdown to header right (except for Profile tab)
      headerRight: route.name !== 'Profile' ? () => (
        <AvatarDropdown 
          currentUser={currentUser} 
          onLogout={handleLogout}
          navigation={navigation}
        />
      ) : undefined,
    };
  }, [theme, currentUser, handleLogout]);

  return (
    <Suspense fallback={<SuspenseFallback />}>
      <Tab.Navigator screenOptions={screenOptions}>
        <Tab.Screen 
          name="Dashboard" 
          component={DashboardScreen}
          options={{
            headerTitle: `Dashboard${currentUser ? ` - ${currentUser.name}` : ''}`,
            headerTitleStyle: {
              fontSize: 18,
              fontWeight: '600' as FontWeight,
            },
          }}
        />
        <Tab.Screen 
          name="Appointments" 
          component={AppointmentsScreen}
          options={{
            headerTitle: `Appointments${currentUser ? ` - ${currentUser.name}` : ''}`,
            headerTitleStyle: {
              fontSize: 18,
              fontWeight: '600' as FontWeight,
            },
          }}
        />
        <Tab.Screen 
          name="Profile" 
          component={ProfileScreen}
          options={{
            headerTitle: `Profile${currentUser ? ` - ${currentUser.name}` : ''}`,
            headerTitleStyle: {
              fontSize: 18,
              fontWeight: '600' as FontWeight,
            },
          }}
        />
      </Tab.Navigator>
    </Suspense>
  );
});

// Main navigator with optimized auth flow and proper TypeScript
export default function AppNavigator() {
  const [isAuthenticated, setIsAuthenticated] = useState<boolean | null>(null);
  const [isLoading, setIsLoading] = useState(true);
  const { theme, isDark } = useTheme();

  useEffect(() => {
    let mounted = true;

    const checkAuthState = async () => {
      try {
        const authenticated = await authService.isAuthenticated();
        if (mounted) setIsAuthenticated(authenticated);
      } catch (error) {
        console.error('Auth check error:', error);
        if (mounted) setIsAuthenticated(false);
      } finally {
        if (mounted) setIsLoading(false);
      }
    };

    // Initial auth check
    checkAuthState();

    // Subscribe to auth state changes
    const unsubscribe = authService.onAuthStateChange((authenticated) => {
      if (mounted) {
        setIsAuthenticated(authenticated);
        setIsLoading(false);
      }
    });

    return () => {
      mounted = false;
      unsubscribe();
    };
  }, []);

  // Fixed navigation theme with proper font weight types
  const navigationTheme = useMemo((): Theme => ({
    dark: isDark,
    colors: {
      ...(isDark ? DarkTheme.colors : DefaultTheme.colors),
      primary: theme.colors.primary,
      background: theme.colors.background,
      card: theme.colors.surface,
      text: theme.colors.text,
      border: theme.colors.border || (isDark ? '#2c2c2e' : '#e0e0e0'),
      notification: theme.colors.primary,
    },
    fonts: {
      regular: {
        fontFamily: Platform.select({
          ios: 'System',
          android: 'Roboto',
          default: 'System',
        }),
        fontWeight: '400' as FontWeight,
      },
      medium: {
        fontFamily: Platform.select({
          ios: 'System',
          android: 'Roboto-Medium',
          default: 'System',
        }),
        fontWeight: '500' as FontWeight,
      },
      bold: {
        fontFamily: Platform.select({
          ios: 'System',
          android: 'Roboto-Bold',
          default: 'System',
        }),
        fontWeight: '700' as FontWeight,
      },
      heavy: {
        fontFamily: Platform.select({
          ios: 'System',
          android: 'Roboto-Black',
          default: 'System',
        }),
        fontWeight: '900' as FontWeight,
      },
    },
  }), [isDark, theme.colors]);

  // Show loading screen while checking auth state
  if (isLoading) {
    return <LoadingScreen />;
  }

  return (
    <NavigationContainer theme={navigationTheme}>
      <Stack.Navigator 
        screenOptions={{ 
          headerShown: false,
          cardStyle: { backgroundColor: theme.colors.background },
          gestureEnabled: true,
          presentation: 'card',
        }}
        initialRouteName={isAuthenticated ? 'Main' : 'Login'}
      >
        {isAuthenticated ? (
          <Stack.Screen 
            name="Main" 
            component={MainTabs} 
            options={{
              headerShown: false,
            }}
          />
        ) : (
          <Stack.Group 
            screenOptions={{
              headerShown: true,
              headerStyle: {
                backgroundColor: theme.colors.surface,
                elevation: Platform.OS === 'android' ? 4 : 0,
                shadowOffset: Platform.OS === 'ios' ? {
                  width: 0,
                  height: 2,
                } : { width: 0, height: 0 },
                shadowRadius: Platform.OS === 'ios' ? 3.84 : 0,
                shadowOpacity: Platform.OS === 'ios' ? 0.25 : 0,
                shadowColor: Platform.OS === 'ios' ? '#000' : 'transparent',
              },
              headerTintColor: theme.colors.text,
              headerTitleStyle: {
                fontSize: 20,
                fontWeight: '600' as FontWeight,
              },
              presentation: 'card',
            }}
          >
            <Stack.Screen 
              name="Login" 
              options={{
                title: 'Sign In',
                headerLeft: () => null,
                gestureEnabled: false,
              }}
            >
              {(props) => (
                <Suspense fallback={<SuspenseFallback />}>
                  <LoginScreen {...props} />
                </Suspense>
              )}
            </Stack.Screen>
            <Stack.Screen 
              name="Register" 
              options={{
                title: 'Sign Up',
                headerBackTitle: 'Back',
              }}
            >
              {(props) => (
                <Suspense fallback={<SuspenseFallback />}>
                  <RegisterScreen {...props} />
                </Suspense>
              )}
            </Stack.Screen>
          </Stack.Group>
        )}
      </Stack.Navigator>
    </NavigationContainer>
  );
}

const styles = StyleSheet.create({
  loadingContainer: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    padding: 20,
  },
  loadingText: {
    marginTop: 16,
    fontSize: 16,
    fontWeight: '500' as FontWeight,
    textAlign: 'center',
  },
  
  // Avatar Container
  avatarContainer: {
    marginRight: 12,
  },
  avatarButton: {
    borderRadius: 18,
    overflow: 'hidden',
  },
  avatar: {
    borderWidth: 2,
    borderColor: 'rgba(255, 255, 255, 0.3)',
  },
  avatarLabel: {
    fontSize: 14,
    fontWeight: '600',
    color: '#FFFFFF',
  },
  
  // Modal Styles
  backdrop: {
    flex: 1,
    backgroundColor: 'rgba(0, 0, 0, 0.5)',
    justifyContent: 'flex-start',
    alignItems: 'flex-end',
  },
  modalContainer: {
    marginTop: Platform.OS === 'android' ? StatusBar.currentHeight + 60 : 100,
    marginRight: 12,
  },
  dropdownCard: {
    borderRadius: 12,
    minWidth: 280,
    maxWidth: screenWidth - 40,
    elevation: 8,
    shadowOffset: {
      width: 0,
      height: 4,
    },
    shadowRadius: 8,
    shadowOpacity: 0.25,
    shadowColor: '#000',
  },
  
  // User Info Header
  userInfoHeader: {
    flexDirection: 'row',
    alignItems: 'center',
    padding: 20,
    paddingBottom: 16,
  },
  headerAvatar: {
    marginRight: 16,
    borderWidth: 2,
    borderColor: 'rgba(255, 255, 255, 0.3)',
  },
  headerAvatarLabel: {
    fontSize: 18,
    fontWeight: '600',
    color: '#FFFFFF',
  },
  userDetails: {
    flex: 1,
  },
  userName: {
    fontSize: 18,
    fontWeight: '600',
    marginBottom: 4,
  },
  userEmail: {
    fontSize: 14,
    marginBottom: 4,
  },
  userType: {
    fontSize: 13,
    fontWeight: '500',
    textTransform: 'capitalize',
  },
  
  // Menu Items
  menuItemsContainer: {
    paddingBottom: 12,
  },
  menuItem: {
    flexDirection: 'row',
    alignItems: 'center',
    paddingHorizontal: 20,
    paddingVertical: 12,
    minHeight: 48,
  },
  menuIcon: {
    marginRight: 16,
    width: 22,
  },
  menuItemText: {
    fontSize: 16,
    fontWeight: '500',
    flex: 1,
  },
  menuDivider: {
    marginVertical: 8,
    marginHorizontal: 20,
    height: 1,
  },
});
```

### 7. Dashboard Screen (`src/screens/dashboard/DashboardScreen.tsx`)

```typescript
import React, { useEffect, useState, useCallback, useMemo } from 'react';
import { View, ScrollView, RefreshControl, Alert } from 'react-native';
import { Card, Title, Paragraph, Button, Text, ActivityIndicator, IconButton } from 'react-native-paper';
import { MaterialCommunityIcons } from '@expo/vector-icons';
import { useTheme } from '../../contexts/ThemeContext';
import { useStyles } from '../../hooks/useStyles';
import healthcareService, { DashboardStats } from '../../services/healthcareService';
import authService from '../../services/authService';

// Memoized stat card component for better performance
const StatCard = React.memo(({ icon, title, value, color = '#2E7D32' }: {
  icon: string;
  title: string;
  value: number | undefined;
  color?: string;
}) => {
  const styles = useStyles((theme) => ({
    statCard: {
      width: '48%',
      marginBottom: theme.spacing.sm,
      ...theme.shadows.medium,
      borderLeftWidth: 4,
      backgroundColor: theme.colors.surface,
      borderRadius: theme.borderRadius.md,
      borderLeftColor: color,
    },
    statContent: {
      paddingVertical: theme.spacing.sm,
    },
    statHeader: {
      flexDirection: 'row',
      justifyContent: 'space-between',
      alignItems: 'center',
      marginBottom: theme.spacing.xs,
    },
    statValue: {
      fontSize: theme.typography.sizes.xl,
      fontWeight: theme.typography.weights.bold,
      fontFamily: theme.typography.fontFamily,
      color,
    },
    statTitle: {
      fontSize: theme.typography.sizes.xs,
      color: theme.colors.textSecondary,
      textAlign: 'center',
      fontFamily: theme.typography.fontFamily,
    },
  }));

  return (
    <Card style={styles.statCard}>
      <Card.Content style={styles.statContent}>
        <View style={styles.statHeader}>
          <MaterialCommunityIcons name={icon as any} size={24} color={color} />
          <Text style={styles.statValue}>{value || 0}</Text>
        </View>
        <Text style={styles.statTitle}>{title}</Text>
      </Card.Content>
    </Card>
  );
});

export default function DashboardScreen({ navigation }: any) {
  const [stats, setStats] = useState<DashboardStats | null>(null);
  const [currentUser, setCurrentUser] = useState<any>(null);
  const [loading, setLoading] = useState(true);
  const [refreshing, setRefreshing] = useState(false);
  const [error, setError] = useState<string | null>(null);
  const { toggleTheme, isDark } = useTheme();

  const styles = useStyles((theme) => ({
    container: {
      flex: 1,
      backgroundColor: theme.colors.background,
      padding: theme.spacing.md,
    },
    loadingContainer: {
      flex: 1,
      justifyContent: 'center',
      alignItems: 'center',
      backgroundColor: theme.colors.background,
    },
    loadingText: {
      marginTop: theme.spacing.md,
      fontSize: theme.typography.sizes.md,
      color: theme.colors.textSecondary,
      fontFamily: theme.typography.fontFamily,
    },
    errorContainer: {
      flex: 1,
      justifyContent: 'center',
      alignItems: 'center',
      padding: theme.spacing.lg,
    },
    errorText: {
      fontSize: theme.typography.sizes.md,
      color: theme.colors.error,
      textAlign: 'center',
      marginBottom: theme.spacing.md,
      fontFamily: theme.typography.fontFamily,
    },
    welcomeCard: {
      marginBottom: theme.spacing.lg,
      ...theme.shadows.medium,
      backgroundColor: theme.colors.surface,
      borderRadius: theme.borderRadius.md,
    },
    welcomeHeader: {
      flexDirection: 'row',
      justifyContent: 'space-between',
      alignItems: 'center',
      marginBottom: theme.spacing.sm,
    },
    welcomeTitle: {
      fontSize: theme.typography.sizes.xl,
      fontWeight: theme.typography.weights.bold,
      color: theme.colors.primary,
      marginBottom: 4,
      fontFamily: theme.typography.fontFamily,
      flex: 1,
    },
    themeToggle: {
      margin: 0,
    },
    welcomeSubtitle: {
      fontSize: theme.typography.sizes.md,
      color: theme.colors.textSecondary,
      marginBottom: theme.spacing.xs,
      fontFamily: theme.typography.fontFamily,
    },
    welcomeDate: {
      fontSize: theme.typography.sizes.sm,
      color: theme.colors.textSecondary,
      fontFamily: theme.typography.fontFamily,
    },
    sectionTitle: {
      fontSize: theme.typography.sizes.lg,
      fontWeight: theme.typography.weights.semibold,
      marginBottom: theme.spacing.md,
      marginTop: theme.spacing.xs,
      color: theme.colors.text,
      fontFamily: theme.typography.fontFamily,
    },
    statsGrid: {
      flexDirection: 'row',
      flexWrap: 'wrap',
      justifyContent: 'space-between',
      marginBottom: theme.spacing.lg,
    },
    actionsGrid: {
      flexDirection: 'row',
      justifyContent: 'space-between',
      marginBottom: theme.spacing.lg,
    },
    actionCard: {
      width: '48%',
      ...theme.shadows.medium,
      backgroundColor: theme.colors.surface,
      borderRadius: theme.borderRadius.md,
    },
    actionContent: {
      alignItems: 'center',
      paddingVertical: theme.spacing.lg,
    },
    actionButton: {
      marginTop: theme.spacing.sm,
      width: '100%',
    },
    activityCard: {
      ...theme.shadows.medium,
      marginBottom: theme.spacing.lg,
      backgroundColor: theme.colors.surface,
      borderRadius: theme.borderRadius.md,
    },
    activityItem: {
      flexDirection: 'row',
      alignItems: 'center',
      paddingVertical: theme.spacing.xs,
    },
    activityText: {
      marginLeft: theme.spacing.sm,
      fontSize: theme.typography.sizes.sm,
      color: theme.colors.textSecondary,
      fontFamily: theme.typography.fontFamily,
    },
  }));

  // Memoized user type display function
  const getUserTypeDisplay = useCallback((userType: string) => {
    switch (userType?.toLowerCase()) {
      case 'healthcare':
        return 'Healthcare Provider';
      case 'doctor':
        return 'Doctor';
      case 'patient':
        return 'Patient';
      case 'admin':
        return 'Administrator';
      default:
        return userType || 'User';
    }
  }, []);

  // Memoized date string
  const currentDateString = useMemo(() => {
    return new Date().toLocaleDateString('en-US', {
      weekday: 'long',
      year: 'numeric',
      month: 'long',
      day: 'numeric'
    });
  }, []);

  // Optimized data loading function
  const loadDashboardData = useCallback(async () => {
    try {
      setError(null);
      
      const [user, dashboardStats] = await Promise.allSettled([
        Promise.resolve(authService.getCurrentUser()),
        healthcareService.getDashboardStats()
      ]);
      
      if (user.status === 'fulfilled') {
        setCurrentUser(user.value);
      }
      
      if (dashboardStats.status === 'fulfilled') {
        setStats(dashboardStats.value);
      } else {
        console.warn('Failed to load dashboard stats:', dashboardStats.reason);
        setError('Failed to load dashboard statistics');
      }
    } catch (error) {
      console.error('Failed to load dashboard data:', error);
      setError('Failed to load dashboard data');
    } finally {
      setLoading(false);
      setRefreshing(false);
    }
  }, []);

  useEffect(() => {
    loadDashboardData();
  }, [loadDashboardData]);

  const onRefresh = useCallback(() => {
    setRefreshing(true);
    loadDashboardData();
  }, [loadDashboardData]);

  const handleRetry = useCallback(() => {
    setLoading(true);
    setError(null);
    loadDashboardData();
  }, [loadDashboardData]);

  // Memoized stats configuration
  const statsConfig = useMemo(() => [
    {
      icon: 'hospital-building',
      title: 'Total Facilities',
      value: stats?.total_facilities,
      color: '#1976D2'
    },
    {
      icon: 'doctor',
      title: 'Connected Doctors',
      value: stats?.connected_doctors,
      color: '#388E3C'
    },
    {
      icon: 'calendar-today',
      title: "Today's Appointments",
      value: stats?.today_appointments,
      color: '#F57C00'
    },
    {
      icon: 'calendar-month',
      title: 'Monthly Appointments',
      value: stats?.monthly_appointments,
      color: '#7B1FA2'
    },
    ...(stats?.pending_requests !== undefined ? [{
      icon: 'clock-outline',
      title: 'Pending Requests',
      value: stats.pending_requests,
      color: '#D32F2F'
    }] : []),
    ...(stats?.total_patients !== undefined ? [{
      icon: 'account-group',
      title: 'Total Patients',
      value: stats.total_patients,
      color: '#00796B'
    }] : [])
  ], [stats]);

  if (loading) {
    return (
      <View style={styles.loadingContainer}>
        <ActivityIndicator size="large" color="#2E7D32" />
        <Text style={styles.loadingText}>Loading Dashboard...</Text>
      </View>
    );
  }

  if (error && !stats) {
    return (
      <View style={styles.errorContainer}>
        <MaterialCommunityIcons name="alert-circle" size={64} color="#F44336" />
        <Text style={styles.errorText}>{error}</Text>
        <Button mode="contained" onPress={handleRetry}>
          Retry
        </Button>
      </View>
    );
  }

  return (
    <ScrollView 
      style={styles.container}
      refreshControl={
        <RefreshControl 
          refreshing={refreshing} 
          onRefresh={onRefresh}
          colors={[isDark ? '#4de352' : '#10d915']}
          progressBackgroundColor={isDark ? '#1e1e1e' : '#ffffff'}
        />
      }
      showsVerticalScrollIndicator={false}
    >
      {/* Welcome Card */}
      <Card style={styles.welcomeCard}>
        <Card.Content>
          <View style={styles.welcomeHeader}>
            <Title style={styles.welcomeTitle}>
              Welcome, {currentUser?.name || 'User'}!
            </Title>
            <IconButton
              icon={isDark ? 'weather-sunny' : 'weather-night'}
              iconColor={isDark ? '#FFA726' : '#2196F3'}
              size={24}
              onPress={toggleTheme}
              style={styles.themeToggle}
            />
          </View>
          <Paragraph style={styles.welcomeSubtitle}>
            {getUserTypeDisplay(currentUser?.user_type)} Dashboard
          </Paragraph>
          <Text style={styles.welcomeDate}>
            {currentDateString}
          </Text>
        </Card.Content>
      </Card>

      {/* Overview Section */}
      <Text style={styles.sectionTitle}>Overview</Text>
      
      <View style={styles.statsGrid}>
        {statsConfig.map((stat, index) => (
          <StatCard
            key={index}
            icon={stat.icon}
            title={stat.title}
            value={stat.value}
            color={stat.color}
          />
        ))}
      </View>

      {/* Quick Actions */}
      <Text style={styles.sectionTitle}>Quick Actions</Text>
      
      <View style={styles.actionsGrid}>
        <Card style={styles.actionCard}>
          <Card.Content style={styles.actionContent}>
            <MaterialCommunityIcons 
              name="calendar-plus" 
              size={32} 
              color={isDark ? '#4de352' : '#2E7D32'} 
            />
            <Button
              mode="outlined"
              onPress={() => navigation.navigate('Appointments')}
              style={styles.actionButton}
            >
              View Appointments
            </Button>
          </Card.Content>
        </Card>

        <Card style={styles.actionCard}>
          <Card.Content style={styles.actionContent}>
            <MaterialCommunityIcons 
              name="account-cog" 
              size={32} 
              color={isDark ? '#4de352' : '#2E7D32'} 
            />
            <Button
              mode="outlined"
              onPress={() => navigation.navigate('Profile')}
              style={styles.actionButton}
            >
              Manage Profile
            </Button>
          </Card.Content>
        </Card>
      </View>

      {/* Recent Activity */}
      <Text style={styles.sectionTitle}>Recent Activity</Text>
      
      <Card style={styles.activityCard}>
        <Card.Content>
          <View style={styles.activityItem}>
            <MaterialCommunityIcons name="check-circle" size={20} color="#4CAF50" />
            <Text style={styles.activityText}>Dashboard loaded successfully</Text>
          </View>
          <View style={styles.activityItem}>
            <MaterialCommunityIcons name="update" size={20} color="#FF9800" />
            <Text style={styles.activityText}>
              Data last updated: {new Date().toLocaleTimeString()}
            </Text>
          </View>
          {error && (
            <View style={styles.activityItem}>
              <MaterialCommunityIcons name="alert" size={20} color="#F44336" />
              <Text style={styles.activityText}>Some data may be incomplete</Text>
            </View>
          )}
        </Card.Content>
      </Card>
    </ScrollView>
  );
}
```

### 8. Appointments Screen (`src/screens/appointments/AppointmentsScreen.tsx`)

```typescript
import React, { useEffect, useState, useCallback } from 'react';
import { View, ScrollView, RefreshControl, Alert, TouchableOpacity, Modal } from 'react-native';
import { Card, Title, Text, Chip, Button, ActivityIndicator, Menu, Divider, TextInput } from 'react-native-paper';
import { MaterialCommunityIcons } from '@expo/vector-icons';
import { useTheme } from '../../contexts/ThemeContext';
import { useStyles } from '../../hooks/useStyles';
import healthcareService from '../../services/healthcareService';

interface Appointment {
  id: number;
  patient_name: string;
  doctor_name: string;
  appointment_date: string;
  appointment_time: string;
  status: string;
  type: string;
  notes?: string;
  email?: string;
  fee?: number;
  clinic?: string;
  specialization?: string;
}

// Helper function to safely convert any value to string
const safeString = (value: any, defaultValue: string = '') => {
  if (value === null || value === undefined) return defaultValue;
  if (typeof value === 'string') return value;
  if (typeof value === 'number') return value.toString();
  if (typeof value === 'boolean') return value.toString();
  return defaultValue;
};

// Format date helper function
const formatDate = (dateString: string) => {
  try {
    if (!dateString || typeof dateString !== 'string') return 'No Date';
    
    const date = new Date(dateString);
    if (isNaN(date.getTime())) return 'Invalid Date';
    
    const today = new Date();
    const tomorrow = new Date(today);
    tomorrow.setDate(today.getDate() + 1);

    if (date.toDateString() === today.toDateString()) {
      return 'Today';
    } else if (date.toDateString() === tomorrow.toDateString()) {
      return 'Tomorrow';
    } else {
      return date.toLocaleDateString('en-US', { 
        month: 'short', 
        day: 'numeric',
        year: date.getFullYear() !== today.getFullYear() ? 'numeric' : undefined
      });
    }
  } catch (error) {
    return 'Invalid Date';
  }
};

// Appointment Details Modal Component
const AppointmentDetailsModal = ({ 
  visible, 
  appointment, 
  onClose, 
  onStatusUpdate 
}: {
  visible: boolean;
  appointment: Appointment | null;
  onClose: () => void;
  onStatusUpdate: (id: number, status: string) => void;
}) => {
  const [updating, setUpdating] = useState(false);
  const { theme } = useTheme();

  const styles = useStyles((theme) => ({
    modalOverlay: {
      flex: 1,
      backgroundColor: 'rgba(0, 0, 0, 0.5)',
      justifyContent: 'center',
      alignItems: 'center',
      padding: 20,
    },
    modalContainer: {
      backgroundColor: theme.colors.surface,
      borderRadius: 12,
      padding: 20,
      width: '100%',
      maxWidth: 400,
      maxHeight: '80%',
    },
    modalHeader: {
      alignItems: 'center',
      marginBottom: 20,
    },
    modalTitle: {
      fontSize: 20,
      fontWeight: 'bold',
      color: theme.colors.text,
      fontFamily: theme.typography.fontFamily,
      marginBottom: 4,
    },
    patientName: {
      fontSize: 16,
      color: theme.colors.text,
      fontFamily: theme.typography.fontFamily,
      fontWeight: '600',
    },
    detailsSection: {
      marginBottom: 20,
    },
    detailRow: {
      marginBottom: 12,
    },
    detailLabel: {
      fontSize: 12,
      color: theme.colors.textSecondary,
      fontFamily: theme.typography.fontFamily,
      marginBottom: 2,
    },
    detailValue: {
      fontSize: 14,
      color: theme.colors.text,
      fontFamily: theme.typography.fontFamily,
      fontWeight: '500',
    },
    doctorInfo: {
      fontSize: 14,
      color: theme.colors.text,
      fontFamily: theme.typography.fontFamily,
    },
    specialization: {
      fontSize: 12,
      color: theme.colors.textSecondary,
      fontFamily: theme.typography.fontFamily,
    },
    symptomsSection: {
      backgroundColor: theme.mode === 'dark' ? 'rgba(255, 255, 255, 0.05)' : '#F8F9FA',
      padding: 12,
      borderRadius: 8,
      marginBottom: 16,
    },
    symptomsTitle: {
      fontSize: 12,
      fontWeight: 'bold',
      color: theme.colors.text,
      marginBottom: 4,
      fontFamily: theme.typography.fontFamily,
    },
    symptomsText: {
      fontSize: 14,
      color: theme.colors.textSecondary,
      fontFamily: theme.typography.fontFamily,
    },
    notesSection: {
      backgroundColor: theme.mode === 'dark' ? 'rgba(255, 255, 255, 0.05)' : '#F0F0F0',
      padding: 12,
      borderRadius: 8,
      marginBottom: 20,
    },
    notesTitle: {
      fontSize: 12,
      fontWeight: 'bold',
      color: theme.colors.text,
      marginBottom: 4,
      fontFamily: theme.typography.fontFamily,
    },
    notesText: {
      fontSize: 14,
      color: theme.colors.textSecondary,
      fontFamily: theme.typography.fontFamily,
      lineHeight: 20,
    },
    actionButtonsContainer: {
      gap: 12,
    },
    actionButton: {
      marginBottom: 8,
    },
    confirmButton: {
      backgroundColor: '#4CAF50',
    },
    cancelButton: {
      backgroundColor: '#F44336',
    },
    closeButton: {
      backgroundColor: 'transparent',
      borderColor: theme.colors.primary,
      borderWidth: 1,
    },
  }));

  const handleStatusUpdate = async (status: string) => {
    if (!appointment) return;

    setUpdating(true);
    try {
      await onStatusUpdate(appointment.id, status);
      onClose();
    } catch (error) {
      // Error handling is done in parent component
    } finally {
      setUpdating(false);
    }
  };

  if (!appointment) return null;

  return (
    <Modal
      visible={visible}
      transparent
      animationType="fade"
      onRequestClose={onClose}
    >
      <View style={styles.modalOverlay}>
        <View style={styles.modalContainer}>
          <ScrollView showsVerticalScrollIndicator={false}>
            {/* Header */}
            <View style={styles.modalHeader}>
              <Text style={styles.modalTitle}>Appointment Details</Text>
              <Text style={styles.patientName}>
                Patient: {safeString(appointment.patient_name, 'Unknown Patient')}
              </Text>
            </View>

            {/* Details Section */}
            <View style={styles.detailsSection}>
              <View style={styles.detailRow}>
                <Text style={styles.detailLabel}>Doctor</Text>
                <Text style={styles.doctorInfo}>
                  Dr. {safeString(appointment.doctor_name, 'sameer soumya ranjan jena')}
                </Text>
                <Text style={styles.specialization}>
                  {safeString(appointment.specialization, 'Cardiology')}
                </Text>
              </View>

              <View style={styles.detailRow}>
                <Text style={styles.detailLabel}>Date & Time</Text>
                <Text style={styles.detailValue}>
                  {formatDate(appointment.appointment_date)} at {safeString(appointment.appointment_time, '09:00 AM')}
                </Text>
              </View>

              <View style={styles.detailRow}>
                <Text style={styles.detailLabel}>Fee</Text>
                <Text style={styles.detailValue}>
                  ₹{safeString(appointment.fee, '500.00')}
                </Text>
              </View>
            </View>

            {/* Symptoms/Reason Section */}
            {appointment.type && (
              <View style={styles.symptomsSection}>
                <Text style={styles.symptomsTitle}>Symptoms/Reason</Text>
                <Text style={styles.symptomsText}>{safeString(appointment.type, 'General consultation')}</Text>
              </View>
            )}

            {/* Read-only Notes Section */}
            {appointment.notes && (
              <View style={styles.notesSection}>
                <Text style={styles.notesTitle}>Notes</Text>
                <Text style={styles.notesText}>{safeString(appointment.notes)}</Text>
              </View>
            )}

            {/* Action Buttons */}
            <View style={styles.actionButtonsContainer}>
              <Button
                mode="contained"
                onPress={() => handleStatusUpdate('confirmed')}
                loading={updating}
                disabled={updating}
                style={[styles.actionButton, styles.confirmButton]}
                icon={() => <MaterialCommunityIcons name="check" size={16} color="white" />}
              >
                Confirm
              </Button>

              <Button
                mode="contained"
                onPress={() => handleStatusUpdate('cancelled')}
                loading={updating}
                disabled={updating}
                style={[styles.actionButton, styles.cancelButton]}
                icon={() => <MaterialCommunityIcons name="close" size={16} color="white" />}
              >
                Cancel
              </Button>

              <Button
                mode="outlined"
                onPress={onClose}
                disabled={updating}
                style={[styles.actionButton, styles.closeButton]}
                labelStyle={{ color: theme.colors.primary }}
              >
                Close
              </Button>
            </View>
          </ScrollView>
        </View>
      </View>
    </Modal>
  );
};

export default function AppointmentsScreen() {
  const [appointments, setAppointments] = useState<Appointment[]>([]);
  const [filteredAppointments, setFilteredAppointments] = useState<Appointment[]>([]);
  const [loading, setLoading] = useState(true);
  const [refreshing, setRefreshing] = useState(false);
  const [filterMenuVisible, setFilterMenuVisible] = useState(false);
  const [selectedFilter, setSelectedFilter] = useState('All');
  const [modalVisible, setModalVisible] = useState(false);
  const [selectedAppointment, setSelectedAppointment] = useState<Appointment | null>(null);

  const { theme } = useTheme();

  const styles = useStyles((theme) => ({
    container: {
      flex: 1,
      backgroundColor: theme.colors.background,
    },
    headerSection: {
      backgroundColor: theme.colors.surface,
      paddingVertical: 16,
      paddingHorizontal: 16,
      borderBottomWidth: 1,
      borderBottomColor: theme.mode === 'dark' ? '#333' : '#E0E0E0',
    },
    title: {
      textAlign: 'center',
      marginBottom: 16,
      color: theme.colors.primary,
      fontSize: 20,
      fontFamily: theme.typography.fontFamily,
      fontWeight: 'bold',
    },
    filterContainer: {
      flexDirection: 'row',
      alignItems: 'center',
      marginBottom: 8,
    },
    filterButton: {
      flexDirection: 'row',
      alignItems: 'center',
      backgroundColor: theme.mode === 'dark' ? '#2C5530' : '#4CAF50',
      paddingHorizontal: 16,
      paddingVertical: 8,
      borderRadius: 4,
      minWidth: 100,
    },
    filterButtonText: {
      color: 'white',
      fontSize: 14,
      fontFamily: theme.typography.fontFamily,
      marginRight: 4,
    },
    contentContainer: {
      flex: 1,
      padding: 16,
    },
    loadingContainer: {
      flex: 1,
      justifyContent: 'center',
      alignItems: 'center',
      backgroundColor: theme.colors.background,
    },
    loadingText: {
      marginTop: 16,
      fontSize: 16,
      color: theme.colors.textSecondary,
      fontFamily: theme.typography.fontFamily,
    },
    emptyCard: {
      marginTop: 50,
      backgroundColor: theme.colors.surface,
      borderRadius: 8,
      elevation: 2,
    },
    emptyContent: {
      alignItems: 'center',
      paddingVertical: 40,
    },
    emptyText: {
      fontSize: 18,
      color: theme.colors.textSecondary,
      marginVertical: 16,
      fontFamily: theme.typography.fontFamily,
    },
    retryButton: {
      marginTop: 8,
    },
    appointmentCard: {
      marginBottom: 16,
      backgroundColor: theme.colors.surface,
      borderRadius: 8,
      elevation: 2,
      shadowColor: '#000',
      shadowOffset: { width: 0, height: 2 },
      shadowOpacity: 0.1,
      shadowRadius: 4,
    },
    cardContent: {
      padding: 16,
    },
    appointmentHeader: {
      flexDirection: 'row',
      justifyContent: 'space-between',
      alignItems: 'flex-start',
      marginBottom: 16,
    },
    patientSection: {
      flex: 1,
      marginRight: 8,
    },
    patientName: {
      fontSize: 18,
      fontWeight: 'bold',
      color: theme.colors.text,
      marginBottom: 4,
      fontFamily: theme.typography.fontFamily,
    },
    patientEmail: {
      fontSize: 14,
      color: theme.colors.textSecondary,
      fontFamily: theme.typography.fontFamily,
      marginBottom: 4,
    },
    statusChip: {
      alignSelf: 'flex-start',
    },
    chipText: {
      color: 'white',
      fontSize: 12,
      fontWeight: 'bold',
      fontFamily: theme.typography.fontFamily,
    },
    appointmentRow: {
      flexDirection: 'row',
      justifyContent: 'space-between',
      marginBottom: 8,
    },
    appointmentColumn: {
      flex: 1,
    },
    columnLabel: {
      fontSize: 12,
      color: theme.colors.textSecondary,
      fontFamily: theme.typography.fontFamily,
      marginBottom: 2,
    },
    columnValue: {
      fontSize: 14,
      color: theme.colors.text,
      fontFamily: theme.typography.fontFamily,
      fontWeight: '500',
    },
    feeText: {
      fontSize: 14,
      color: theme.mode === 'dark' ? '#4CAF50' : '#2E7D32',
      fontFamily: theme.typography.fontFamily,
      fontWeight: 'bold',
    },
    notesSection: {
      backgroundColor: theme.mode === 'dark' ? 'rgba(255, 255, 255, 0.05)' : '#F0F0F0',
      padding: 8,
      borderRadius: 4,
      marginBottom: 16,
    },
    notesLabel: {
      fontSize: 12,
      fontWeight: 'bold',
      color: theme.colors.text,
      marginBottom: 4,
      fontFamily: theme.typography.fontFamily,
    },
    notesText: {
      fontSize: 14,
      color: theme.colors.textSecondary,
      fontFamily: theme.typography.fontFamily,
    },
    actionButtons: {
      flexDirection: 'row',
      justifyContent: 'center',
      marginTop: 8,
    },
    viewDetailsButton: {
      flex: 1,
      backgroundColor: 'transparent',
      borderColor: theme.mode === 'dark' ? '#4CAF50' : '#2E7D32',
      borderWidth: 1,
    },
    divider: {
      marginVertical: 8,
      backgroundColor: theme.mode === 'dark' ? '#333' : '#E0E0E0',
    },
  }));

  useEffect(() => {
    fetchAppointments();
  }, []);

  useEffect(() => {
    filterAppointments();
  }, [selectedFilter, appointments]);

  const fetchAppointments = async () => {
    try {
      const data = await healthcareService.getAppointments();
      const appointmentsArray = Array.isArray(data) ? data : (data?.appointments || []);
      setAppointments(appointmentsArray);
    } catch (error) {
      console.error('Failed to fetch appointments:', error);
      Alert.alert('Error', 'Failed to load appointments');
      setAppointments([]);
    } finally {
      setLoading(false);
      setRefreshing(false);
    }
  };

  const filterAppointments = () => {
    if (selectedFilter === 'All') {
      setFilteredAppointments(appointments);
    } else {
      const filtered = appointments.filter(appointment => {
        const status = appointment.status;
        if (typeof status === 'string') {
          return status.toLowerCase() === selectedFilter.toLowerCase();
        }
        return false;
      });
      setFilteredAppointments(filtered);
    }
  };

  const handleViewDetails = (appointment: Appointment) => {
    setSelectedAppointment(appointment);
    setModalVisible(true);
  };

  const handleStatusUpdate = async (appointmentId: number, status: string) => {
    try {
      const response = await healthcareService.updateAppointmentStatus(appointmentId, status);
      
      // Update the local state
      setAppointments(prev => 
        prev.map(appointment => 
          appointment.id === appointmentId 
            ? { ...appointment, status }
            : appointment
        )
      );

      Alert.alert(
        'Success', 
        `Appointment ${status} successfully!`,
        [{ text: 'OK' }]
      );
    } catch (error: any) {
      console.error('Failed to update appointment status:', error);
      Alert.alert(
        'Error', 
        error.message || 'Failed to update appointment status'
      );
      throw error; // Re-throw to handle in modal
    }
  };

  const onRefresh = useCallback(() => {
    setRefreshing(true);
    fetchAppointments();
  }, []);

  const getStatusColor = (status: string) => {
    if (!status || typeof status !== 'string') return '#757575';
    
    const statusLower = status.toLowerCase();
    switch (statusLower) {
      case 'confirmed':
      case 'pending':
        return '#FF9800';
      case 'cancelled':
        return '#F44336';
      case 'completed':
        return '#2196F3';
      default:
        return '#757575';
    }
  };

  const getStatusIcon = (status: string) => {
    if (!status || typeof status !== 'string') return 'help-circle';
    
    const statusLower = status.toLowerCase();
    switch (statusLower) {
      case 'confirmed':
      case 'pending':
        return 'clock';
      case 'cancelled':
        return 'cancel';
      case 'completed':
        return 'check-circle-outline';
      default:
        return 'help-circle';
    }
  };

  if (loading) {
    return (
      <View style={styles.loadingContainer}>
        <ActivityIndicator size="large" color="#2E7D32" />
        <Text style={styles.loadingText}>Loading Appointments...</Text>
      </View>
    );
  }

  const filterOptions = ['All', 'Pending', 'Confirmed', 'Completed', 'Cancelled'];

  return (
    <View style={styles.container}>
      {/* Header Section */}
      <View style={styles.headerSection}>
        <Text style={styles.title}>Clinic Appointments</Text>
        
        <View style={styles.filterContainer}>
          <Menu
            visible={filterMenuVisible}
            onDismiss={() => setFilterMenuVisible(false)}
            anchor={
              <TouchableOpacity
                style={styles.filterButton}
                onPress={() => setFilterMenuVisible(true)}
              >
                <Text style={styles.filterButtonText}>{selectedFilter}</Text>
                <MaterialCommunityIcons 
                  name="chevron-down" 
                  size={16} 
                  color="white" 
                />
              </TouchableOpacity>
            }
          >
            {filterOptions.map((option) => (
              <Menu.Item
                key={option}
                onPress={() => {
                  setSelectedFilter(option);
                  setFilterMenuVisible(false);
                }}
                title={option}
                titleStyle={{
                  color: selectedFilter === option ? theme.colors.primary : theme.colors.text
                }}
              />
            ))}
          </Menu>
        </View>
      </View>

      {/* Content Section */}
      <ScrollView 
        style={styles.contentContainer}
        refreshControl={
          <RefreshControl refreshing={refreshing} onRefresh={onRefresh} />
        }
        showsVerticalScrollIndicator={false}
      >
        {filteredAppointments.length === 0 ? (
          <Card style={styles.emptyCard}>
            <Card.Content style={styles.emptyContent}>
              <MaterialCommunityIcons name="calendar-blank" size={64} color="#CCC" />
              <Text style={styles.emptyText}>
                {selectedFilter === 'All' ? 'No appointments found' : `No ${selectedFilter.toLowerCase()} appointments`}
              </Text>
              <Button mode="outlined" onPress={fetchAppointments} style={styles.retryButton}>
                Refresh
              </Button>
            </Card.Content>
          </Card>
        ) : (
          filteredAppointments.map((appointment, index) => (
            <Card key={`appointment-${appointment.id || index}`} style={styles.appointmentCard}>
              <View style={styles.cardContent}>
                {/* Header with Patient Info and Status */}
                <View style={styles.appointmentHeader}>
                  <View style={styles.patientSection}>
                    <Text style={styles.patientName}>
                      {safeString(appointment.patient_name, 'Unknown Patient')}
                    </Text>
                    <Text style={styles.patientEmail}>
                      {safeString(appointment.email, 'sameerjena@gmail.com')}
                    </Text>
                  </View>
                  <Chip
                    icon={() => (
                      <MaterialCommunityIcons 
                        name={getStatusIcon(appointment.status)} 
                        size={14} 
                        color="white" 
                      />
                    )}
                    style={[
                      styles.statusChip,
                      { backgroundColor: getStatusColor(appointment.status) }
                    ]}
                    textStyle={styles.chipText}
                  >
                    {safeString(appointment.status, 'Unknown').charAt(0).toUpperCase() + safeString(appointment.status, 'unknown').slice(1)}
                  </Chip>
                </View>

                {/* Appointment Details Row */}
                <View style={styles.appointmentRow}>
                  <View style={styles.appointmentColumn}>
                    <Text style={styles.columnLabel}>Date & Time</Text>
                    <Text style={styles.columnValue}>
                      {formatDate(appointment.appointment_date)}
                    </Text>
                    <Text style={styles.columnValue}>
                      {safeString(appointment.appointment_time, '09:00 AM')}
                    </Text>
                  </View>
                  
                  <View style={styles.appointmentColumn}>
                    <Text style={styles.columnLabel}>Doctor</Text>
                    <Text style={styles.columnValue}>
                      Dr. {safeString(appointment.doctor_name, 'sameer soumya ranjan jena')}
                    </Text>
                    <Text style={[styles.columnValue, { fontSize: 12 }]}>
                      {safeString(appointment.specialization, 'Cardiology')}
                    </Text>
                  </View>
                  
                  <View style={[styles.appointmentColumn, { alignItems: 'flex-end' }]}>
                    <Text style={styles.columnLabel}>Fee</Text>
                    <Text style={styles.feeText}>
                      ₹{safeString(appointment.fee, '500.00')}
                    </Text>
                  </View>
                </View>

                {/* Notes Section */}
                {appointment.notes && (
                  <View style={styles.notesSection}>
                    <Text style={styles.notesLabel}>Notes:</Text>
                    <Text style={styles.notesText}>{safeString(appointment.notes)}</Text>
                  </View>
                )}

                <Divider style={styles.divider} />

                {/* Action Buttons - Only View Details for all appointments */}
                <View style={styles.actionButtons}>
                  <Button 
                    mode="outlined" 
                    onPress={() => handleViewDetails(appointment)}
                    style={styles.viewDetailsButton}
                    labelStyle={{ color: theme.mode === 'dark' ? '#4CAF50' : '#2E7D32' }}
                    icon={() => (
                      <MaterialCommunityIcons 
                        name="eye" 
                        size={16} 
                        color={theme.mode === 'dark' ? '#4CAF50' : '#2E7D32'} 
                      />
                    )}
                  >
                    View Details
                  </Button>
                </View>
              </View>
            </Card>
          ))
        )}
      </ScrollView>

      {/* Appointment Details Modal */}
      <AppointmentDetailsModal
        visible={modalVisible}
        appointment={selectedAppointment}
        onClose={() => {
          setModalVisible(false);
          setSelectedAppointment(null);
        }}
        onStatusUpdate={handleStatusUpdate}
      />
    </View>
  );
}
```

### 9. Profile Screen (`src/screens/profile/ProfileScreen.tsx`)

```typescript
import React, { useEffect, useState } from 'react';
import { View, ScrollView, Alert } from 'react-native';
import { Card, Title, Text, Button, Avatar, Divider, List, IconButton, Menu, Switch } from 'react-native-paper';
import { MaterialCommunityIcons } from '@expo/vector-icons';
import { useTheme } from '../../contexts/ThemeContext';
import { useStyles } from '../../hooks/useStyles';
import authService from '../../services/authService';
import healthcareService from '../../services/healthcareService';

export default function ProfileScreen({ navigation }: any) {
  const [user, setUser] = useState<any>(null);
  const [loading, setLoading] = useState(false);
  const [menuVisible, setMenuVisible] = useState(false);
  const { toggleTheme, isDark, setTheme, themeMode } = useTheme();

  const styles = useStyles((theme) => ({
    container: {
      flex: 1,
      backgroundColor: theme.colors.background,
      padding: theme.spacing.md,
    },
    loadingContainer: {
      flex: 1,
      justifyContent: 'center',
      alignItems: 'center',
      backgroundColor: theme.colors.background,
    },
    profileCard: {
      marginBottom: theme.spacing.md,
      ...theme.shadows.medium,
      backgroundColor: theme.colors.surface,
      borderRadius: theme.borderRadius.md,
    },
    profileContent: {
      alignItems: 'center',
      paddingVertical: theme.spacing.lg,
    },
    profileHeader: {
      flexDirection: 'row',
      justifyContent: 'space-between',
      alignItems: 'center',
      width: '100%',
      marginBottom: theme.spacing.md,
    },
    avatarSection: {
      alignItems: 'center',
      flex: 1,
    },
    themeToggle: {
      margin: 0,
    },
    avatar: {
      backgroundColor: theme.colors.primary,
      marginBottom: theme.spacing.md,
    },
    userName: {
      fontSize: theme.typography.sizes.xl,
      fontWeight: theme.typography.weights.bold,
      color: theme.colors.text,
      marginBottom: 4,
      fontFamily: theme.typography.fontFamily,
    },
    userType: {
      fontSize: theme.typography.sizes.md,
      color: theme.colors.primary,
      marginBottom: 4,
      fontWeight: theme.typography.weights.medium,
      fontFamily: theme.typography.fontFamily,
    },
    userEmail: {
      fontSize: theme.typography.sizes.sm,
      color: theme.colors.textSecondary,
      fontFamily: theme.typography.fontFamily,
    },
    detailsCard: {
      marginBottom: theme.spacing.md,
      ...theme.shadows.medium,
      backgroundColor: theme.colors.surface,
      borderRadius: theme.borderRadius.md,
    },
    sectionTitle: {
      fontSize: theme.typography.sizes.lg,
      marginBottom: theme.spacing.md,
      color: theme.colors.text,
      fontFamily: theme.typography.fontFamily,
      fontWeight: theme.typography.weights.semibold,
    },
    detailRow: {
      flexDirection: 'row',
      alignItems: 'center',
      paddingVertical: theme.spacing.sm,
    },
    detailContent: {
      marginLeft: theme.spacing.md,
      flex: 1,
    },
    detailLabel: {
      fontSize: theme.typography.sizes.xs,
      color: theme.colors.textSecondary,
      marginBottom: 2,
      textTransform: 'uppercase',
      fontFamily: theme.typography.fontFamily,
      fontWeight: theme.typography.weights.medium,
    },
    detailValue: {
      fontSize: theme.typography.sizes.md,
      color: theme.colors.text,
      fontFamily: theme.typography.fontFamily,
    },
    divider: {
      marginVertical: 4,
      backgroundColor: theme.colors.divider,
    },
    themeCard: {
      marginBottom: theme.spacing.md,
      ...theme.shadows.medium,
      backgroundColor: theme.colors.surface,
      borderRadius: theme.borderRadius.md,
    },
    themeRow: {
      flexDirection: 'row',
      alignItems: 'center',
      justifyContent: 'space-between',
      paddingVertical: theme.spacing.sm,
    },
    themeLabel: {
      fontSize: theme.typography.sizes.md,
      color: theme.colors.text,
      fontFamily: theme.typography.fontFamily,
      flex: 1,
    },
    themeDescription: {
      fontSize: theme.typography.sizes.sm,
      color: theme.colors.textSecondary,
      fontFamily: theme.typography.fontFamily,
      marginTop: 2,
    },
    themeOptions: {
      marginTop: theme.spacing.sm,
    },
    themeOption: {
      flexDirection: 'row',
      alignItems: 'center',
      justifyContent: 'space-between',
      paddingVertical: theme.spacing.xs,
    },
    themeOptionText: {
      fontSize: theme.typography.sizes.sm,
      color: theme.colors.text,
      fontFamily: theme.typography.fontFamily,
      marginLeft: theme.spacing.sm,
    },
    actionsCard: {
      marginBottom: theme.spacing.md,
      ...theme.shadows.medium,
      backgroundColor: theme.colors.surface,
      borderRadius: theme.borderRadius.md,
    },
    actionButton: {
      marginBottom: theme.spacing.sm,
    },
    logoutButton: {
      marginTop: theme.spacing.xs,
    },
    infoCard: {
      ...theme.shadows.small,
      marginBottom: theme.spacing.lg,
      backgroundColor: theme.colors.surface,
      borderRadius: theme.borderRadius.md,
    },
    appInfo: {
      textAlign: 'center',
      fontSize: theme.typography.sizes.xs,
      color: theme.colors.textSecondary,
      marginBottom: 4,
      fontFamily: theme.typography.fontFamily,
    },
    menuAnchor: {
      position: 'relative',
    },
  }));

  useEffect(() => {
    loadUserProfile();
  }, []);

  const loadUserProfile = async () => {
    try {
      const currentUser = await authService.getCurrentUser();
      setUser(currentUser);
      
      // Try to get more detailed profile from API
      try {
        const profileData = await healthcareService.getProfile();
        if (profileData?.user) {
          setUser(profileData.user);
        }
      } catch (profileError) {
        console.log('Could not fetch extended profile data:', profileError);
      }
    } catch (error) {
      console.error('Failed to load profile:', error);
    }
  };

  const handleLogout = async () => {
    Alert.alert(
      'Logout',
      'Are you sure you want to logout?',
      [
        { text: 'Cancel', style: 'cancel' },
        {
          text: 'Logout',
          style: 'destructive',
          onPress: async () => {
            setLoading(true);
            try {
              await authService.logout();
              // Navigation will be handled by auth state change
            } catch (error) {
              console.error('Logout error:', error);
            } finally {
              setLoading(false);
            }
          }
        }
      ]
    );
  };

  const getUserTypeDisplay = (userType: string) => {
    switch (userType?.toLowerCase()) {
      case 'healthcare':
        return 'Healthcare Provider';
      case 'doctor':
        return 'Doctor';
      case 'patient':
        return 'Patient';
      case 'admin':
        return 'Administrator';
      default:
        return userType || 'User';
    }
  };

  const getThemeModeDisplay = (mode: string) => {
    switch (mode) {
      case 'light':
        return 'Light Mode';
      case 'dark':
        return 'Dark Mode';
      case 'system':
        return 'System Default';
      default:
        return 'System Default';
    }
  };

  if (!user) {
    return (
      <View style={styles.loadingContainer}>
        <Text style={styles.userName}>Loading profile...</Text>
      </View>
    );
  }

  return (
    <ScrollView style={styles.container} showsVerticalScrollIndicator={false}>
      {/* Profile Header */}
      <Card style={styles.profileCard}>
        <Card.Content style={styles.profileContent}>
          <View style={styles.profileHeader}>
            <View style={styles.avatarSection}>
              <Avatar.Text 
                size={80} 
                label={user.name?.charAt(0)?.toUpperCase() || 'U'} 
                style={styles.avatar}
              />
              <Title style={styles.userName}>{user.name || 'User'}</Title>
              <Text style={styles.userType}>{getUserTypeDisplay(user.user_type)}</Text>
              <Text style={styles.userEmail}>{user.email}</Text>
            </View>
            <IconButton
              icon={isDark ? 'weather-sunny' : 'weather-night'}
              iconColor={isDark ? '#FFA726' : '#2196F3'}
              size={24}
              onPress={toggleTheme}
              style={styles.themeToggle}
            />
          </View>
        </Card.Content>
      </Card>

      {/* Theme Settings */}
      <Card style={styles.themeCard}>
        <Card.Content>
          <Title style={styles.sectionTitle}>Theme Settings</Title>
          
          <View style={styles.themeRow}>
            <View style={{ flex: 1 }}>
              <Text style={styles.themeLabel}>Dark Mode</Text>
              <Text style={styles.themeDescription}>
                Toggle between light and dark themes
              </Text>
            </View>
            <Switch
              value={isDark}
              onValueChange={toggleTheme}
              trackColor={{ 
                false: isDark ? '#767577' : '#767577', 
                true: isDark ? '#4de352' : '#10d915' 
              }}
              thumbColor={isDark ? '#f4f3f4' : '#f4f3f4'}
            />
          </View>

          <Divider style={styles.divider} />

          <View style={styles.themeOptions}>
            <Text style={styles.themeLabel}>Theme Mode</Text>
            
            <View style={styles.themeOption}>
              <View style={{ flexDirection: 'row', alignItems: 'center', flex: 1 }}>
                <MaterialCommunityIcons 
                  name="weather-sunny" 
                  size={20} 
                  color={themeMode === 'light' ? '#FFA726' : (isDark ? '#FFF' : '#666')} 
                />
                <Text style={styles.themeOptionText}>Light</Text>
              </View>
              <Button
                mode={themeMode === 'light' ? 'contained' : 'outlined'}
                onPress={() => setTheme('light')}
                compact
              >
                {themeMode === 'light' ? 'Active' : 'Select'}
              </Button>
            </View>

            <View style={styles.themeOption}>
              <View style={{ flexDirection: 'row', alignItems: 'center', flex: 1 }}>
                <MaterialCommunityIcons 
                  name="weather-night" 
                  size={20} 
                  color={themeMode === 'dark' ? '#2196F3' : (isDark ? '#FFF' : '#666')} 
                />
                <Text style={styles.themeOptionText}>Dark</Text>
              </View>
              <Button
                mode={themeMode === 'dark' ? 'contained' : 'outlined'}
                onPress={() => setTheme('dark')}
                compact
              >
                {themeMode === 'dark' ? 'Active' : 'Select'}
              </Button>
            </View>

            <View style={styles.themeOption}>
              <View style={{ flexDirection: 'row', alignItems: 'center', flex: 1 }}>
                <MaterialCommunityIcons 
                  name="theme-light-dark" 
                  size={20} 
                  color={themeMode === 'system' ? '#9C27B0' : (isDark ? '#FFF' : '#666')} 
                />
                <Text style={styles.themeOptionText}>System</Text>
              </View>
              <Button
                mode={themeMode === 'system' ? 'contained' : 'outlined'}
                onPress={() => setTheme('system')}
                compact
              >
                {themeMode === 'system' ? 'Active' : 'Select'}
              </Button>
            </View>
          </View>
        </Card.Content>
      </Card>

      {/* Profile Details */}
      <Card style={styles.detailsCard}>
        <Card.Content>
          <Title style={styles.sectionTitle}>Account Information</Title>
          
          <View style={styles.detailRow}>
            <MaterialCommunityIcons name="account" size={20} color={isDark ? '#FFF' : '#666'} />
            <View style={styles.detailContent}>
              <Text style={styles.detailLabel}>Full Name</Text>
              <Text style={styles.detailValue}>{user.name || 'Not provided'}</Text>
            </View>
          </View>

          <Divider style={styles.divider} />

          <View style={styles.detailRow}>
            <MaterialCommunityIcons name="email" size={20} color={isDark ? '#FFF' : '#666'} />
            <View style={styles.detailContent}>
              <Text style={styles.detailLabel}>Email Address</Text>
              <Text style={styles.detailValue}>{user.email}</Text>
            </View>
          </View>

          <Divider style={styles.divider} />

          <View style={styles.detailRow}>
            <MaterialCommunityIcons name="phone" size={20} color={isDark ? '#FFF' : '#666'} />
            <View style={styles.detailContent}>
              <Text style={styles.detailLabel}>Phone Number</Text>
              <Text style={styles.detailValue}>{user.phone || 'Not provided'}</Text>
            </View>
          </View>

          <Divider style={styles.divider} />

          <View style={styles.detailRow}>
            <MaterialCommunityIcons name="badge-account" size={20} color={isDark ? '#FFF' : '#666'} />
            <View style={styles.detailContent}>
              <Text style={styles.detailLabel}>User Type</Text>
              <Text style={styles.detailValue}>{getUserTypeDisplay(user.user_type)}</Text>
            </View>
          </View>

          {user.address && (
            <>
              <Divider style={styles.divider} />
              <View style={styles.detailRow}>
                <MaterialCommunityIcons name="map-marker" size={20} color={isDark ? '#FFF' : '#666'} />
                <View style={styles.detailContent}>
                  <Text style={styles.detailLabel}>Address</Text>
                  <Text style={styles.detailValue}>{user.address}</Text>
                </View>
              </View>
            </>
          )}

          <Divider style={styles.divider} />

          <View style={styles.detailRow}>
            <MaterialCommunityIcons name="palette" size={20} color={isDark ? '#FFF' : '#666'} />
            <View style={styles.detailContent}>
              <Text style={styles.detailLabel}>Current Theme</Text>
              <Text style={styles.detailValue}>{getThemeModeDisplay(themeMode)} ({isDark ? 'Dark' : 'Light'})</Text>
            </View>
          </View>
        </Card.Content>
      </Card>

      {/* Action Buttons */}
      <Card style={styles.actionsCard}>
        <Card.Content>
          <Title style={styles.sectionTitle}>Account Actions</Title>
          
          <Button
            mode="outlined"
            icon="account-edit"
            onPress={() => Alert.alert('Info', 'Edit profile functionality will be implemented')}
            style={styles.actionButton}
          >
            Edit Profile
          </Button>

          <Button
            mode="outlined"
            icon="lock-reset"
            onPress={() => Alert.alert('Info', 'Change password functionality will be implemented')}
            style={styles.actionButton}
          >
            Change Password
          </Button>

          <Button
            mode="outlined"
            icon="bell-cog"
            onPress={() => Alert.alert('Info', 'Notification settings functionality will be implemented')}
            style={styles.actionButton}
          >
            Notification Settings
          </Button>

          <Button
            mode="outlined"
            icon="palette"
            onPress={() => Alert.alert(
              'Theme Info', 
              `Current theme: ${getThemeModeDisplay(themeMode)}\nActive mode: ${isDark ? 'Dark' : 'Light'}\n\nUse the theme controls above to customize your experience.`
            )}
            style={styles.actionButton}
          >
            Theme Information
          </Button>

          <Button
            mode="contained"
            icon="logout"
            onPress={handleLogout}
            loading={loading}
            style={[styles.actionButton, styles.logoutButton]}
            buttonColor={isDark ? '#cf6679' : '#F44336'}
          >
            Logout
          </Button>
        </Card.Content>
      </Card>

      {/* App Info */}
      <Card style={styles.infoCard}>
        <Card.Content>
          <Text style={styles.appInfo}>Visit Care Healthcare v1.0.0</Text>
          <Text style={styles.appInfo}>© 2024 Visit Care. All rights reserved.</Text>
          <Text style={styles.appInfo}>
            Theme: {getThemeModeDisplay(themeMode)} • {isDark ? 'Dark Mode' : 'Light Mode'}
          </Text>
        </Card.Content>
      </Card>
    </ScrollView>
  );
}
```

### 10. App.tsx 

```typescript
import React from 'react';
import { PaperProvider } from 'react-native-paper';
import { StatusBar } from 'expo-status-bar';
import { ThemeProvider, useTheme } from './src/contexts/ThemeContext';
import AppNavigator from './src/navigation/AppNavigator';

const AppContent = () => {
  const { theme, isDark } = useTheme();
  
  const paperTheme = {
    colors: {
      primary: theme.colors.primary,
      secondary: theme.colors.secondary,
      surface: theme.colors.surface,
      background: theme.colors.background,
      error: theme.colors.error,
      text: theme.colors.text,
      onPrimary: theme.colors.onPrimary,
      onSecondary: theme.colors.onSecondary,
      onSurface: theme.colors.onSurface,
      onBackground: theme.colors.onBackground,
      outline: theme.colors.border,
    },
  };

  return (
    <PaperProvider theme={paperTheme}>
      <StatusBar style={isDark ? 'light' : 'dark'} backgroundColor={theme.colors.background} />
      <AppNavigator />
    </PaperProvider>
  );
};

export default function App() {
  return (
    <ThemeProvider>
      <AppContent />
    </ThemeProvider>
  );
}
```
### 11. Theme (`src/constants/theme.ts`)

```typescript
export interface ThemeColors {
  primary: string;
  primaryLight: string;
  primaryDark: string;
  secondary: string;
  secondaryLight: string;
  secondaryDark: string;
  background: string;
  surface: string;
  error: string;
  warning: string;
  info: string;
  success: string;
  onPrimary: string;
  onSecondary: string;
  onBackground: string;
  onSurface: string;
  onError: string;
  text: string;
  textSecondary: string;
  border: string;
  divider: string;
  disabled: string;
  placeholder: string;
}

export interface Theme {
  colors: ThemeColors;
  mode: 'light' | 'dark';
  spacing: {
    xs: number;
    sm: number;
    md: number;
    lg: number;
    xl: number;
    xxl: number;
  };
  typography: {
    fontFamily: string;
    sizes: {
      xs: number;
      sm: number;
      md: number;
      lg: number;
      xl: number;
      xxl: number;
    };
    weights: {
      light: '200';
      normal: '300';
      regular: '400';
      medium: '500';
      semibold: '600';
      bold: '700';
    };
  };
  shadows: {
    small: object;
    medium: object;
    large: object;
  };
  borderRadius: {
    xs: number;
    sm: number;
    md: number;
    lg: number;
    xl: number;
  };
}

const baseSpacing = {
  xs: 4,
  sm: 8,
  md: 16,
  lg: 24,
  xl: 32,
  xxl: 48,
};

const baseTypography = {
  fontFamily: 'Poppins',
  sizes: {
    xs: 12,
    sm: 14,
    md: 16,
    lg: 18,
    xl: 24,
    xxl: 32,
  },
  weights: {
    light: '200' as const,
    normal: '300' as const,
    regular: '400' as const,
    medium: '500' as const,
    semibold: '600' as const,
    bold: '700' as const,
  },
};

const baseShadows = {
  small: {
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 2 },
    shadowOpacity: 0.1,
    shadowRadius: 4,
    elevation: 2,
  },
  medium: {
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 4 },
    shadowOpacity: 0.15,
    shadowRadius: 8,
    elevation: 4,
  },
  large: {
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 8 },
    shadowOpacity: 0.2,
    shadowRadius: 16,
    elevation: 8,
  },
};

const baseBorderRadius = {
  xs: 4,
  sm: 8,
  md: 12,
  lg: 16,
  xl: 24,
};

export const lightTheme: Theme = {
  colors: {
    primary: '#10d915',
    primaryLight: '#4de352',
    primaryDark: '#0cb010',
    secondary: '#dc004e',
    secondaryLight: '#ff5983',
    secondaryDark: '#9a0036',
    background: '#f5f5f5',
    surface: '#ffffff',
    error: '#f44336',
    warning: '#ff9800',
    info: '#2196f3',
    success: '#4caf50',
    onPrimary: '#ffffff',
    onSecondary: '#ffffff',
    onBackground: '#000000',
    onSurface: '#000000',
    onError: '#ffffff',
    text: 'rgba(0, 0, 0, 0.87)',
    textSecondary: 'rgba(0, 0, 0, 0.6)',
    border: 'rgba(0, 0, 0, 0.12)',
    divider: 'rgba(0, 0, 0, 0.12)',
    disabled: 'rgba(0, 0, 0, 0.26)',
    placeholder: 'rgba(0, 0, 0, 0.38)',
  },
  mode: 'light',
  spacing: baseSpacing,
  typography: baseTypography,
  shadows: baseShadows,
  borderRadius: baseBorderRadius,
};

export const darkTheme: Theme = {
  colors: {
    primary: '#10d915',
    primaryLight: '#4de352',
    primaryDark: '#0cb010',
    secondary: '#dc004e',
    secondaryLight: '#ff5983',
    secondaryDark: '#9a0036',
    background: '#121212',
    surface: '#1e1e1e',
    error: '#cf6679',
    warning: '#ffb74d',
    info: '#81c784',
    success: '#66bb6a',
    onPrimary: '#000000',
    onSecondary: '#000000',
    onBackground: '#ffffff',
    onSurface: '#ffffff',
    onError: '#000000',
    text: '#ffffff',
    textSecondary: 'rgba(255, 255, 255, 0.7)',
    border: 'rgba(255, 255, 255, 0.12)',
    divider: 'rgba(255, 255, 255, 0.12)',
    disabled: 'rgba(255, 255, 255, 0.38)',
    placeholder: 'rgba(255, 255, 255, 0.5)',
  },
  mode: 'dark',
  spacing: baseSpacing,
  typography: baseTypography,
  shadows: {
    small: {
      ...baseShadows.small,
      shadowColor: '#000',
      shadowOpacity: 0.3,
    },
    medium: {
      ...baseShadows.medium,
      shadowColor: '#000',
      shadowOpacity: 0.4,
    },
    large: {
      ...baseShadows.large,
      shadowColor: '#000',
      shadowOpacity: 0.5,
    },
  },
  borderRadius: baseBorderRadius,
};
```

### 12. Theme Context (`src/contexts/ThemeContext.tsx`)

```typescript
import React, { createContext, useContext, useState, useEffect, ReactNode, useCallback } from 'react';
import AsyncStorage from '@react-native-async-storage/async-storage';
import { Appearance, ColorSchemeName } from 'react-native';
import { lightTheme, darkTheme, Theme } from '../constants/theme';

interface ThemeContextType {
  theme: Theme;
  isDark: boolean;
  toggleTheme: () => void;
  setTheme: (theme: 'light' | 'dark' | 'system') => void;
  themeMode: 'light' | 'dark' | 'system';
}

const ThemeContext = createContext<ThemeContextType | undefined>(undefined);

export const useTheme = () => {
  const context = useContext(ThemeContext);
  if (!context) {
    throw new Error('useTheme must be used within a ThemeProvider');
  }
  return context;
};

interface ThemeProviderProps {
  children: ReactNode;
}

export const ThemeProvider: React.FC<ThemeProviderProps> = ({ children }) => {
  const [themeMode, setThemeMode] = useState<'light' | 'dark' | 'system'>('system');
  const [currentTheme, setCurrentTheme] = useState<Theme>(lightTheme);

  // Memoized theme calculation
  const calculateTheme = useCallback((mode: 'light' | 'dark' | 'system', systemScheme?: ColorSchemeName) => {
    if (mode === 'system') {
      const scheme = systemScheme || Appearance.getColorScheme();
      return scheme === 'dark' ? darkTheme : lightTheme;
    }
    return mode === 'dark' ? darkTheme : lightTheme;
  }, []);

  useEffect(() => {
    let mounted = true;

    const loadThemePreference = async () => {
      try {
        const savedTheme = await AsyncStorage.getItem('theme_preference');
        const preference = (savedTheme as 'light' | 'dark' | 'system') || 'system';
        
        if (mounted) {
          setThemeMode(preference);
          setCurrentTheme(calculateTheme(preference));
        }
      } catch (error) {
        console.error('Error loading theme preference:', error);
        if (mounted) {
          setCurrentTheme(lightTheme);
        }
      }
    };

    loadThemePreference();

    return () => {
      mounted = false;
    };
  }, [calculateTheme]);

  useEffect(() => {
    if (themeMode !== 'system') return;

    const subscription = Appearance.addChangeListener(({ colorScheme }) => {
      setCurrentTheme(calculateTheme('system', colorScheme));
    });

    return () => subscription?.remove();
  }, [themeMode, calculateTheme]);

  const toggleTheme = useCallback(() => {
    const newMode = currentTheme.mode === 'light' ? 'dark' : 'light';
    setTheme(newMode);
  }, [currentTheme.mode]);

  const setTheme = useCallback(async (mode: 'light' | 'dark' | 'system') => {
    try {
      setThemeMode(mode);
      await AsyncStorage.setItem('theme_preference', mode);
      setCurrentTheme(calculateTheme(mode));
    } catch (error) {
      console.error('Error saving theme preference:', error);
    }
  }, [calculateTheme]);

  const value: ThemeContextType = {
    theme: currentTheme,
    isDark: currentTheme.mode === 'dark',
    toggleTheme,
    setTheme,
    themeMode,
  };

  return (
    <ThemeContext.Provider value={value}>
      {children}
    </ThemeContext.Provider>
  );
};
```
### 13. Hook For Theme (`src/hook/useStyles.ts`)

```typescript
import { useMemo } from 'react';
import { StyleSheet } from 'react-native';
import { useTheme } from '../contexts/ThemeContext';
import { Theme } from '../constants/theme';

// Optimized styles hook with better memoization
export const useStyles = <T extends StyleSheet.NamedStyles<T>>(
  stylesFn: (theme: Theme) => T
): T => {
  const { theme } = useTheme();
  
  return useMemo(() => {
    return StyleSheet.create(stylesFn(theme));
  }, [theme, stylesFn]);
};

// Performance monitoring hook
export const usePerformance = () => {
  const startTime = useMemo(() => Date.now(), []);
  
  const measureRender = useMemo(() => (componentName: string) => {
    const endTime = Date.now();
    const renderTime = endTime - startTime;
    
    if (__DEV__ && renderTime > 100) {
      console.warn(`Slow render detected in ${componentName}: ${renderTime}ms`);
    }
    
    return renderTime;
  }, [startTime]);

  return { measureRender };
};
```

