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
│   │   │   └── LanguageSwitcher.tsx
│   ├── constants/
│   │   │   ├── locales.ts
│   │   │   └── theme.ts
│   ├── contexts/
│   │   │   ├── LanguageContext.tsx
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
import axios from 'axios';
import AsyncStorage from '@react-native-async-storage/async-storage';

const API_BASE_URL = __DEV__
  ? 'http://10.0.2.2:8000/api' // Android Emulator
  : 'https://your-production-domain.com/api';

const apiClient = axios.create({
  baseURL: API_BASE_URL,
  timeout: 30000,
  headers: {
    'Content-Type': 'application/json',
    'Accept': 'application/json',
  },
});

// Request interceptor for auth tokens + language header
apiClient.interceptors.request.use(async (config) => {
  // Auth token
  const token =
    (await AsyncStorage.getItem('auth_token')) || // your original key
    (await AsyncStorage.getItem('authtoken'));   // handle both keys if needed
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }

  // Language header
  const language = (await AsyncStorage.getItem('appLanguage')) || 'en';
  config.headers['x-app-language'] = language;
  config.headers['accept-language'] = language;

  return config;
});

// Response interceptor for error handling
apiClient.interceptors.response.use(
  (response) => response,
  async (error) => {
    if (error.response?.status === 401) {
      await AsyncStorage.multiRemove(['auth_token', 'user_data']);
      // optionally: navigate to login, show toast, etc
    }
    return Promise.reject(error);
  }
);

export default apiClient;
```

### 2. Authentication Service (`src/services/authService.ts`)

```typescript
import apiClient from './api';
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

class AuthService {
  async login(credentials: LoginCredentials) {
    try {
      console.log('Attempting login with:', { email: credentials.email });
      
      const response = await apiClient.post('/login', credentials);
      console.log('Login response:', response.data);
      
      const { token, user } = response.data;
      
      if (!token || !user) {
        throw new Error('Invalid response format from server');
      }
      
      await AsyncStorage.setItem('auth_token', token);
      await AsyncStorage.setItem('user_data', JSON.stringify(user));
      
      console.log('Auth data saved successfully');
      
      return response.data;
    } catch (error: any) {
      console.error('Login error:', error);
      
      if (error.response) {
        const errorMessage = error.response.data?.message || 
                           error.response.data?.error || 
                           'Login failed';
        throw new Error(errorMessage);
      } else if (error.request) {
        throw new Error('Network error - please check your connection');
      } else {
        throw new Error(error.message || 'Unknown error occurred');
      }
    }
  }

  async register(data: RegisterData) {
    try {
      console.log('Attempting registration with:', { 
        email: data.email, 
        user_type: data.user_type 
      });
      
      const response = await apiClient.post('/register', data);
      console.log('Registration response:', response.data);
      
      const { token, user } = response.data;
      
      if (!token || !user) {
        throw new Error('Invalid response format from server');
      }
      
      await AsyncStorage.setItem('auth_token', token);
      await AsyncStorage.setItem('user_data', JSON.stringify(user));
      
      return response.data;
    } catch (error: any) {
      console.error('Registration error:', error);
      
      if (error.response) {
        const errorMessage = error.response.data?.message || 
                           error.response.data?.error || 
                           'Registration failed';
        throw new Error(errorMessage);
      } else if (error.request) {
        throw new Error('Network error - please check your connection');
      } else {
        throw new Error(error.message || 'Unknown error occurred');
      }
    }
  }

  async logout() {
    try {
      console.log('Attempting logout');
      await apiClient.post('/logout');
    } catch (error) {
      console.error('Logout API error:', error);
    } finally {
      await AsyncStorage.multiRemove(['auth_token', 'user_data']);
      console.log('Auth data cleared');
    }
  }

  async getCurrentUser() {
    try {
      const userData = await AsyncStorage.getItem('user_data');
      return userData ? JSON.parse(userData) : null;
    } catch (error) {
      console.error('Error getting current user:', error);
      return null;
    }
  }

  async isAuthenticated() {
    try {
      const token = await AsyncStorage.getItem('auth_token');
      return !!token;
    } catch (error) {
      console.error('Error checking auth state:', error);
      return false;
    }
  }

  async getAuthToken() {
    try {
      return await AsyncStorage.getItem('auth_token');
    } catch (error) {
      console.error('Error getting auth token:', error);
      return null;
    }
  }
}

export default new AuthService();
```

### 3. Healthcare Service (`src/services/healthcareService.ts`)

```typescript
import apiClient from './api';

class HealthcareService {
  async getDashboardStats() {
    try {
      const response = await apiClient.get('/healthcare/dashboard-stats');
      return response.data;
    } catch (error) {
      console.error('Failed to fetch dashboard stats:', error);
      throw error;
    }
  }

  async getProfile() {
    try {
      const response = await apiClient.get('/healthcare/profile');
      return response.data;
    } catch (error) {
      console.error('Failed to fetch profile:', error);
      throw error;
    }
  }

  async updateProfile(data: FormData) {
    try {
      const response = await apiClient.post('/healthcare/profile', data, {
        headers: { 'Content-Type': 'multipart/form-data' }
      });
      return response.data;
    } catch (error) {
      console.error('Failed to update profile:', error);
      throw error;
    }
  }

  async getAppointments(page = 1, filters = {}) {
    try {
      const params = new URLSearchParams();
      params.append('page', page.toString());
      
      Object.entries(filters).forEach(([key, value]) => {
        if (value) params.append(key, value as string);
      });
      
      const response = await apiClient.get(`/healthcare/appointments?${params}`);
      return response.data;
    } catch (error) {
      console.error('Failed to fetch appointments:', error);
      throw error;
    }
  }

  async getConnectionRequests() {
    try {
      const response = await apiClient.get('/healthcare/connection-requests');
      return response.data;
    } catch (error) {
      console.error('Failed to fetch connection requests:', error);
      throw error;
    }
  }

  async respondToConnectionRequest(requestId: number, status: string, data = {}) {
    try {
      const response = await apiClient.post(`/healthcare/connection-requests/${requestId}/respond`, {
        status,
        ...data
      });
      return response.data;
    } catch (error) {
      console.error('Failed to respond to connection request:', error);
      throw error;
    }
  }

  async getConnectedDoctors() {
    try {
      const response = await apiClient.get('/healthcare/connected-doctors');
      return response.data;
    } catch (error) {
      console.error('Failed to fetch connected doctors:', error);
      throw error;
    }
  }
}

export default new HealthcareService();
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
import React, { useEffect, useState } from 'react';
import { View, Text, StyleSheet, ActivityIndicator } from 'react-native';
import { NavigationContainer, DefaultTheme, DarkTheme } from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
import { Button } from 'react-native-paper';
import { MaterialCommunityIcons } from '@expo/vector-icons';

import { useTheme } from '../contexts/ThemeContext';
import authService from '../services/authService';

import LoginScreen from '../screens/auth/LoginScreen';
import RegisterScreen from '../screens/auth/RegisterScreen';
import DashboardScreen from '../screens/dashboard/DashboardScreen';
import AppointmentsScreen from '../screens/appointments/AppointmentsScreen';
import ProfileScreen from '../screens/profile/ProfileScreen';

const Stack = createStackNavigator();
const Tab = createBottomTabNavigator();

function LoadingScreen() {
  return (
    <View style={styles.loadingContainer}>
      <ActivityIndicator size="large" />
      <Text style={styles.loadingText}>Loading...</Text>
    </View>
  );
}

function MainTabs() {
  const [currentUser, setCurrentUser] = useState<any>(null);

  useEffect(() => {
    const loadCurrentUser = async () => {
      const user = await authService.getCurrentUser();
      setCurrentUser(user);
    };
    loadCurrentUser();
  }, []);

  const handleLogout = async () => {
    await authService.logout();
    // Auth state auto-updates, navigation will change via AppNavigator
  };

  const { theme, isDark } = useTheme();

  return (
    <Tab.Navigator
      screenOptions={({ route }) => ({
        tabBarIcon: ({ focused, color, size }) => {
          let iconName: keyof typeof MaterialCommunityIcons.glyphMap = 'circle';
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
          }
          // @ts-ignore
          return <MaterialCommunityIcons name={iconName} size={size} color={color} />;
        },
        tabBarActiveTintColor: '#2E7D32',
        tabBarInactiveTintColor: 'gray',
        headerRight: () =>
          route.name === 'Profile' ? null : (
            <Button mode="text" onPress={handleLogout} style={{ marginRight: 10 }}>
              Logout
            </Button>
          ),
        headerTitle: route.name + (currentUser ? ` - ${currentUser.name}` : ''),
        tabBarStyle: { backgroundColor: theme.colors.surface },
      })}
    >
      <Tab.Screen name="Dashboard" component={DashboardScreen} />
      <Tab.Screen name="Appointments" component={AppointmentsScreen} />
      <Tab.Screen name="Profile" component={ProfileScreen} />
    </Tab.Navigator>
  );
}

export default function AppNavigator() {
  const [isAuthenticated, setIsAuthenticated] = useState<boolean | null>(null);
  const [isLoading, setIsLoading] = useState(true);
  const { theme, isDark } = useTheme();

  useEffect(() => {
    // Listen for auth state changes
    let unmounted = false;
    const checkAuthState = async () => {
      try {
        const authenticated = await authService.isAuthenticated();
        if (!unmounted) setIsAuthenticated(authenticated);
      } catch (error) {
        if (!unmounted) setIsAuthenticated(false);
      } finally {
        if (!unmounted && isLoading) setIsLoading(false);
      }
    };
    checkAuthState();
    const interval = setInterval(checkAuthState, 1000);
    return () => {
      unmounted = true;
      clearInterval(interval);
    };
  }, []);

  if (isLoading) return <LoadingScreen />;

  return (
    <NavigationContainer 
      theme={{
        ...((isDark ? DarkTheme : DefaultTheme) as any),
        colors: {
          ...((isDark ? DarkTheme : DefaultTheme).colors),
          ...theme.colors,
        },
      }}
    >
      <Stack.Navigator screenOptions={{ headerShown: false }}>
        {isAuthenticated ? (
          <Stack.Screen name="Main" component={MainTabs} />
        ) : (
          <>
            <Stack.Screen name="Login" component={LoginScreen} />
            <Stack.Screen name="Register" component={RegisterScreen} />
          </>
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
    backgroundColor: '#F5F7FA',
  },
  loadingText: {
    marginTop: 16,
    fontSize: 16,
    color: '#666',
  },
});
```

### 7. Dashboard Screen (`src/screens/dashboard/DashboardScreen.tsx`)

```typescript
import React, { useEffect, useState, useCallback } from 'react';
import { View, ScrollView, RefreshControl, Alert } from 'react-native';
import { Card, Title, Paragraph, Button, Text, ActivityIndicator, IconButton } from 'react-native-paper';
import { MaterialCommunityIcons } from '@expo/vector-icons';
import { useTheme } from '../../contexts/ThemeContext';
import { useStyles } from '../../hooks/useStyles';
import healthcareService from '../../services/healthcareService';
import authService from '../../services/authService';

interface DashboardStats {
  total_facilities?: number;
  connected_doctors?: number;
  today_appointments?: number;
  monthly_appointments?: number;
  pending_requests?: number;
  total_patients?: number;
}

export default function DashboardScreen({ navigation }: any) {
  const [stats, setStats] = useState<DashboardStats | null>(null);
  const [currentUser, setCurrentUser] = useState<any>(null);
  const [loading, setLoading] = useState(true);
  const [refreshing, setRefreshing] = useState(false);
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
    statCard: {
      width: '48%',
      marginBottom: theme.spacing.sm,
      ...theme.shadows.medium,
      borderLeftWidth: 4,
      backgroundColor: theme.colors.surface,
      borderRadius: theme.borderRadius.md,
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
    },
    statTitle: {
      fontSize: theme.typography.sizes.xs,
      color: theme.colors.textSecondary,
      textAlign: 'center',
      fontFamily: theme.typography.fontFamily,
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

  useEffect(() => {
    loadDashboardData();
  }, []);

  const loadDashboardData = async () => {
    try {
      const [user, dashboardStats] = await Promise.all([
        authService.getCurrentUser(),
        fetchDashboardStats()
      ]);
      
      setCurrentUser(user);
      setStats(dashboardStats);
    } catch (error) {
      console.error('Failed to load dashboard data:', error);
      Alert.alert('Error', 'Failed to load dashboard data');
    } finally {
      setLoading(false);
      setRefreshing(false);
    }
  };

  const fetchDashboardStats = async () => {
    try {
      const data = await healthcareService.getDashboardStats();
      return data;
    } catch (error: any) {
      if (error.response?.status === 401) {
        Alert.alert('Session Expired', 'Please log in again');
        await authService.logout();
        return null;
      }
      throw error;
    }
  };

  const onRefresh = useCallback(() => {
    setRefreshing(true);
    loadDashboardData();
  }, []);

  const StatCard = ({ icon, title, value, color = '#2E7D32' }: any) => (
    <Card style={[styles.statCard, { borderLeftColor: color }]}>
      <Card.Content style={styles.statContent}>
        <View style={styles.statHeader}>
          <MaterialCommunityIcons name={icon} size={24} color={color} />
          <Text style={[styles.statValue, { color }]}>{value || 0}</Text>
        </View>
        <Text style={styles.statTitle}>{title}</Text>
      </Card.Content>
    </Card>
  );

  if (loading) {
    return (
      <View style={styles.loadingContainer}>
        <ActivityIndicator size="large" color="#2E7D32" />
        <Text style={styles.loadingText}>Loading Dashboard...</Text>
      </View>
    );
  }

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
    >
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
            {new Date().toLocaleDateString('en-US', {
              weekday: 'long',
              year: 'numeric',
              month: 'long',
              day: 'numeric'
            })}
          </Text>
        </Card.Content>
      </Card>

      <Text style={styles.sectionTitle}>Overview</Text>
      
      <View style={styles.statsGrid}>
        <StatCard
          icon="hospital-building"
          title="Total Facilities"
          value={stats?.total_facilities}
          color="#1976D2"
        />
        
        <StatCard
          icon="doctor"
          title="Connected Doctors"
          value={stats?.connected_doctors}
          color="#388E3C"
        />
        
        <StatCard
          icon="calendar-today"
          title="Today's Appointments"
          value={stats?.today_appointments}
          color="#F57C00"
        />
        
        <StatCard
          icon="calendar-month"
          title="Monthly Appointments"
          value={stats?.monthly_appointments}
          color="#7B1FA2"
        />

        {stats?.pending_requests !== undefined && (
          <StatCard
            icon="clock-outline"
            title="Pending Requests"
            value={stats.pending_requests}
            color="#D32F2F"
          />
        )}

        {stats?.total_patients !== undefined && (
          <StatCard
            icon="account-group"
            title="Total Patients"
            value={stats.total_patients}
            color="#00796B"
          />
        )}
      </View>

      <Text style={styles.sectionTitle}>Quick Actions</Text>
      
      <View style={styles.actionsGrid}>
        <Card style={styles.actionCard}>
          <Card.Content style={styles.actionContent}>
            <MaterialCommunityIcons name="calendar-plus" size={32} color={isDark ? '#4de352' : '#2E7D32'} />
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
            <MaterialCommunityIcons name="account-cog" size={32} color={isDark ? '#4de352' : '#2E7D32'} />
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

      <Text style={styles.sectionTitle}>Recent Activity</Text>
      
      <Card style={styles.activityCard}>
        <Card.Content>
          <View style={styles.activityItem}>
            <MaterialCommunityIcons name="check-circle" size={20} color="#4CAF50" />
            <Text style={styles.activityText}>Dashboard loaded successfully</Text>
          </View>
          <View style={styles.activityItem}>
            <MaterialCommunityIcons name="update" size={20} color="#FF9800" />
            <Text style={styles.activityText}>Data last updated: {new Date().toLocaleTimeString()}</Text>
          </View>
        </Card.Content>
      </Card>
    </ScrollView>
  );
}
```

### 8. Appointments Screen (`src/screens/appointments/AppointmentsScreen.tsx`)

```typescript
import React, { useEffect, useState, useCallback } from 'react';
import { View, ScrollView, RefreshControl, Alert } from 'react-native';
import { Card, Title, Text, Chip, Button, ActivityIndicator } from 'react-native-paper';
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
}

export default function AppointmentsScreen() {
  const [appointments, setAppointments] = useState<Appointment[]>([]);
  const [loading, setLoading] = useState(true);
  const [refreshing, setRefreshing] = useState(false);

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
    title: {
      textAlign: 'center',
      marginBottom: theme.spacing.lg,
      color: theme.colors.primary,
      fontSize: theme.typography.sizes.xl,
      fontFamily: theme.typography.fontFamily,
    },
    emptyCard: {
      ...theme.shadows.medium,
      marginTop: 50,
      backgroundColor: theme.colors.surface,
      borderRadius: theme.borderRadius.md,
    },
    emptyContent: {
      alignItems: 'center',
      paddingVertical: 40,
    },
    emptyText: {
      fontSize: theme.typography.sizes.lg,
      color: theme.colors.textSecondary,
      marginVertical: theme.spacing.lg,
      fontFamily: theme.typography.fontFamily,
    },
    retryButton: {
      marginTop: theme.spacing.sm,
    },
    appointmentCard: {
      marginBottom: theme.spacing.md,
      ...theme.shadows.medium,
      backgroundColor: theme.colors.surface,
      borderRadius: theme.borderRadius.md,
    },
    appointmentHeader: {
      flexDirection: 'row',
      justifyContent: 'space-between',
      alignItems: 'flex-start',
      marginBottom: theme.spacing.md,
    },
    appointmentInfo: {
      flex: 1,
    },
    patientName: {
      fontSize: theme.typography.sizes.lg,
      fontWeight: theme.typography.weights.bold,
      color: theme.colors.text,
      marginBottom: 4,
      fontFamily: theme.typography.fontFamily,
    },
    doctorName: {
      fontSize: theme.typography.sizes.sm,
      color: theme.colors.textSecondary,
      fontFamily: theme.typography.fontFamily,
    },
    statusChip: {
      marginLeft: theme.spacing.xs,
    },
    chipText: {
      color: 'white',
      fontSize: theme.typography.sizes.xs,
      fontWeight: theme.typography.weights.bold,
      fontFamily: theme.typography.fontFamily,
    },
    appointmentDetails: {
      marginBottom: theme.spacing.md,
    },
    detailItem: {
      flexDirection: 'row',
      alignItems: 'center',
      marginBottom: theme.spacing.xs,
    },
    detailText: {
      marginLeft: theme.spacing.xs,
      fontSize: theme.typography.sizes.sm,
      color: theme.colors.textSecondary,
      fontFamily: theme.typography.fontFamily,
    },
    notesSection: {
      backgroundColor: theme.mode === 'dark' ? 'rgba(255, 255, 255, 0.05)' : '#F0F0F0',
      padding: theme.spacing.sm,
      borderRadius: theme.borderRadius.sm,
      marginBottom: theme.spacing.md,
    },
    notesLabel: {
      fontSize: theme.typography.sizes.xs,
      fontWeight: theme.typography.weights.bold,
      color: theme.colors.text,
      marginBottom: 4,
      fontFamily: theme.typography.fontFamily,
    },
    notesText: {
      fontSize: theme.typography.sizes.sm,
      color: theme.colors.textSecondary,
      fontFamily: theme.typography.fontFamily,
    },
    actionButtons: {
      flexDirection: 'row',
      justifyContent: 'space-between',
    },
    actionButton: {
      flex: 1,
      marginHorizontal: 4,
    },
  }));

  useEffect(() => {
    fetchAppointments();
  }, []);

  const fetchAppointments = async () => {
    try {
      const data = await healthcareService.getAppointments();
      setAppointments(data.appointments || []);
    } catch (error) {
      console.error('Failed to fetch appointments:', error);
      Alert.alert('Error', 'Failed to load appointments');
    } finally {
      setLoading(false);
      setRefreshing(false);
    }
  };

  const onRefresh = useCallback(() => {
    setRefreshing(true);
    fetchAppointments();
  }, []);

  const getStatusColor = (status: string) => {
    switch (status?.toLowerCase()) {
      case 'confirmed':
        return '#4CAF50';
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
    switch (status?.toLowerCase()) {
      case 'confirmed':
        return 'check-circle';
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

  return (
    <ScrollView 
      style={styles.container}
      refreshControl={
        <RefreshControl refreshing={refreshing} onRefresh={onRefresh} />
      }
    >
      <Title style={styles.title}>Appointments Management</Title>

      {appointments.length === 0 ? (
        <Card style={styles.emptyCard}>
          <Card.Content style={styles.emptyContent}>
            <MaterialCommunityIcons name="calendar-blank" size={64} color="#CCC" />
            <Text style={styles.emptyText}>No appointments found</Text>
            <Button mode="outlined" onPress={fetchAppointments} style={styles.retryButton}>
              Refresh
            </Button>
          </Card.Content>
        </Card>
      ) : (
        appointments.map((appointment) => (
          <Card key={appointment.id} style={styles.appointmentCard}>
            <Card.Content>
              <View style={styles.appointmentHeader}>
                <View style={styles.appointmentInfo}>
                  <Text style={styles.patientName}>
                    {appointment.patient_name || 'Unknown Patient'}
                  </Text>
                  <Text style={styles.doctorName}>
                    Dr. {appointment.doctor_name || 'Unknown Doctor'}
                  </Text>
                </View>
                <Chip
                  icon={() => (
                    <MaterialCommunityIcons 
                      name={getStatusIcon(appointment.status)} 
                      size={16} 
                      color="white" 
                    />
                  )}
                  style={[
                    styles.statusChip,
                    { backgroundColor: getStatusColor(appointment.status) }
                  ]}
                  textStyle={styles.chipText}
                >
                  {appointment.status?.toUpperCase() || 'UNKNOWN'}
                </Chip>
              </View>

              <View style={styles.appointmentDetails}>
                <View style={styles.detailItem}>
                  <MaterialCommunityIcons name="calendar" size={16} color="#666" />
                  <Text style={styles.detailText}>
                    {new Date(appointment.appointment_date).toLocaleDateString()}
                  </Text>
                </View>
                
                <View style={styles.detailItem}>
                  <MaterialCommunityIcons name="clock" size={16} color="#666" />
                  <Text style={styles.detailText}>
                    {appointment.appointment_time}
                  </Text>
                </View>
                
                <View style={styles.detailItem}>
                  <MaterialCommunityIcons name="medical-bag" size={16} color="#666" />
                  <Text style={styles.detailText}>
                    {appointment.type || 'General Consultation'}
                  </Text>
                </View>
              </View>

              {appointment.notes && (
                <View style={styles.notesSection}>
                  <Text style={styles.notesLabel}>Notes:</Text>
                  <Text style={styles.notesText}>{appointment.notes}</Text>
                </View>
              )}

              <View style={styles.actionButtons}>
                <Button 
                  mode="outlined" 
                  onPress={() => Alert.alert('Info', 'View details functionality')}
                  style={styles.actionButton}
                >
                  View Details
                </Button>
                {appointment.status === 'pending' && (
                  <Button 
                    mode="contained" 
                    onPress={() => Alert.alert('Info', 'Update status functionality')}
                    style={styles.actionButton}
                  >
                    Update Status
                  </Button>
                )}
              </View>
            </Card.Content>
          </Card>
        ))
      )}
    </ScrollView>
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
import { LanguageProvider } from './src/contexts/LanguageContext';
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
    <LanguageProvider>
      <ThemeProvider>
        <AppContent />
      </ThemeProvider>
    </LanguageProvider>
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
import React, { createContext, useContext, useState, useEffect, ReactNode } from 'react';
import AsyncStorage from '@react-native-async-storage/async-storage';
import { Appearance } from 'react-native';
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

  useEffect(() => {
    loadThemePreference();
    
    const subscription = Appearance.addChangeListener(({ colorScheme }) => {
      if (themeMode === 'system') {
        setCurrentTheme(colorScheme === 'dark' ? darkTheme : lightTheme);
      }
    });

    return () => subscription?.remove();
  }, [themeMode]);

  const loadThemePreference = async () => {
    try {
      const savedTheme = await AsyncStorage.getItem('theme_preference');
      const preference = (savedTheme as 'light' | 'dark' | 'system') || 'system';
      setThemeMode(preference);
      
      if (preference === 'system') {
        const systemScheme = Appearance.getColorScheme();
        setCurrentTheme(systemScheme === 'dark' ? darkTheme : lightTheme);
      } else {
        setCurrentTheme(preference === 'dark' ? darkTheme : lightTheme);
      }
    } catch (error) {
      console.error('Error loading theme preference:', error);
    }
  };

  const toggleTheme = () => {
    const newMode = currentTheme.mode === 'light' ? 'dark' : 'light';
    setTheme(newMode);
  };

  const setTheme = async (mode: 'light' | 'dark' | 'system') => {
    try {
      setThemeMode(mode);
      await AsyncStorage.setItem('theme_preference', mode);
      
      if (mode === 'system') {
        const systemScheme = Appearance.getColorScheme();
        setCurrentTheme(systemScheme === 'dark' ? darkTheme : lightTheme);
      } else {
        setCurrentTheme(mode === 'dark' ? darkTheme : lightTheme);
      }
    } catch (error) {
      console.error('Error saving theme preference:', error);
    }
  };

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

export const useStyles = <T extends StyleSheet.NamedStyles<T>>(
  stylesFn: (theme: Theme) => T
): T => {
  const { theme } = useTheme();
  
  return useMemo(() => {
    return StyleSheet.create(stylesFn(theme));
  }, [theme, stylesFn]);
};
```
### 12. Language Context (`src/contexts/LanguageContext.tsx`)

```typescript
import React, { createContext, useContext, useState, useEffect, ReactNode } from 'react';
import AsyncStorage from '@react-native-async-storage/async-storage';

type Language = 'en' | 'od';

interface LanguageContextType {
  language: Language;
  setLanguage: (lang: Language) => void;
}

const LanguageContext = createContext<LanguageContextType | undefined>(undefined);

export const useLanguage = () => {
  const context = useContext(LanguageContext);
  if (!context) throw new Error('useLanguage must be used within a LanguageProvider');
  return context;
};

export const LanguageProvider: React.FC<{children: ReactNode}> = ({ children }) => {
  const [language, setLanguageState] = useState<Language>('en');

  useEffect(() => {
    const loadLanguage = async () => {
      const stored = await AsyncStorage.getItem('appLanguage');
      if (stored) setLanguageState(stored as Language);
    };
    loadLanguage();
  }, []);

  const setLanguage = async (lang: Language) => {
    setLanguageState(lang);
    await AsyncStorage.setItem('appLanguage', lang);
  };

  return (
    <LanguageContext.Provider value={{ language, setLanguage }}>
      {children}
    </LanguageContext.Provider>
  );
};
```
### 12. locales (`src/constants/locales.ts`)

```typescript
export const translations = {
  en: {
    welcome: "Welcome",
    dashboard: "Dashboard",
    appointments: "Appointments",
    profile: "Profile",
  },
  od: {
    welcome: "ସ୍ବାଗତ",
    dashboard: "ଡାଶବୋର୍ଡ",
    appointments: "ଲିଖାପିଏ",
    profile: "ପ୍ରଫାଇଲ୍",
  },
};
```
### 12. Language Switcher (`src/components/LanguageSwitcher.tsx`)

```typescript
import React from 'react';
import { View } from 'react-native';
import { Button, Title } from 'react-native-paper';
import { useLanguage } from '../contexts/LanguageContext';

const LanguageSwitcher: React.FC = () => {
  const { language, setLanguage } = useLanguage();
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', marginVertical: 10 }}>
      <Title style={{ marginRight: 16 }}>Language</Title>
      <Button mode={language === 'en' ? 'contained' : 'outlined'} onPress={() => setLanguage('en')}>English</Button>
      <Button mode={language === 'od' ? 'contained' : 'outlined'} onPress={() => setLanguage('od')} style={{ marginLeft: 8 }}>Odia</Button>
    </View>
  );
};
export default LanguageSwitcher;
```
