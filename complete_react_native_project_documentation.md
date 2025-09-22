# Complete React Native Healthcare App Documentation

## Table of Contents
1. [Project Overview](#project-overview)
2. [Problem Analysis & Solution](#problem-analysis--solution)
3. [Project Structure](#project-structure)
4. [Installation & Setup Guide](#installation--setup-guide)
5. [Complete Source Code](#complete-source-code)
6. [Network Configuration Guide](#network-configuration-guide)
7. [API Integration](#api-integration)
8. [Debugging & Troubleshooting](#debugging--troubleshooting)
9. [Deployment Instructions](#deployment-instructions)

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

## Problem Analysis & Solution

### Initial Problems Encountered

1. **Network Connection Issue**
   - **Problem**: `Login Failed - Invalid credentials` error
   - **Root Cause**: Android emulator couldn't connect to `localhost`
   - **Solution**: Configure API base URL to use `10.0.2.2` for Android emulator

2. **Navigation Error**
   - **Problem**: `The action 'REPLACE' with payload {"name":"Main"} was not handled by any navigator`
   - **Root Cause**: Missing 'Main' screen in navigation structure
   - **Solution**: Added proper nested navigation with 'Main' screen

3. **Authentication Flow Issues**
   - **Problem**: Token storage and retrieval problems
   - **Solution**: Enhanced AsyncStorage implementation with proper error handling

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
│   │   ├── common/
│   │   │   ├── StatCard.tsx
│   │   │   └── LoadingScreen.tsx
│   │   └── ui/
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
│   └── types/
│       └── index.ts
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

### 1. API Configuration (`src/services/api.ts`)

```typescript
import axios from 'axios';
import AsyncStorage from '@react-native-async-storage/async-storage';

// Network configuration for different environments
const API_BASE_URL = __DEV__ 
  ? 'http://10.0.2.2:8000/api'  // Android Emulator
  // ? 'http://localhost:8000/api'  // iOS Simulator
  // ? 'http://192.168.1.100:8000/api'  // Physical Device
  : 'https://your-production-domain.com/api';

console.log('API Base URL:', API_BASE_URL);

const apiClient = axios.create({
  baseURL: API_BASE_URL,
  timeout: 30000,
  headers: {
    'Content-Type': 'application/json',
    'Accept': 'application/json',
  },
});

// Request interceptor
apiClient.interceptors.request.use(
  async (config) => {
    console.log(`API Request: ${config.method?.toUpperCase()} ${config.url}`);
    
    const token = await AsyncStorage.getItem('auth_token');
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
      console.log('Token added to request');
    }
    
    const language = await AsyncStorage.getItem('app_language') || 'en';
    config.headers['Accept-Language'] = language;
    
    return config;
  },
  (error) => {
    console.error('Request interceptor error:', error);
    return Promise.reject(error);
  }
);

// Response interceptor
apiClient.interceptors.response.use(
  (response) => {
    console.log(`API Response: ${response.status} - ${response.config.url}`);
    return response;
  },
  async (error) => {
    console.error('API Error:', {
      url: error.config?.url,
      status: error.response?.status,
      message: error.response?.data?.message || error.message,
      data: error.response?.data
    });
    
    if (error.response?.status === 401) {
      console.log('Unauthorized - clearing auth data');
      await AsyncStorage.multiRemove(['auth_token', 'user_data']);
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
import { View, StyleSheet, Alert, ScrollView, KeyboardAvoidingView, Platform } from 'react-native';
import { TextInput, Button, Card, Title, Text, HelperText } from 'react-native-paper';
import { useForm, Controller } from 'react-hook-form';
import authService, { LoginCredentials } from '../../services/authService';

interface LoginScreenProps {
  navigation: any;
}

export default function LoginScreen({ navigation }: LoginScreenProps) {
  const [loading, setLoading] = useState(false);
  const [showPassword, setShowPassword] = useState(false);
  
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
        [{ text: 'OK', onPress: () => navigation.replace('Main') }]
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
              >
                {loading ? 'Logging in...' : 'Login'}
              </Button>
              
              <Button
                mode="text"
                onPress={() => navigation.navigate('Register')}
                disabled={loading}
                style={styles.linkButton}
              >
                Don't have an account? Register
              </Button>
            </Card.Content>
          </Card>
        </View>
      </ScrollView>
    </KeyboardAvoidingView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#F5F7FA'
  },
  scrollContent: {
    flexGrow: 1,
    justifyContent: 'center'
  },
  content: {
    flex: 1,
    padding: 16,
    justifyContent: 'center'
  },
  card: {
    padding: 16,
    elevation: 4,
    borderRadius: 12
  },
  title: {
    textAlign: 'center',
    marginBottom: 8,
    color: '#2E7D32',
    fontSize: 24,
    fontWeight: 'bold'
  },
  subtitle: {
    textAlign: 'center',
    marginBottom: 32,
    color: '#666',
    fontSize: 16
  },
  input: {
    marginBottom: 8
  },
  button: {
    marginTop: 24,
    marginBottom: 16
  },
  buttonContent: {
    paddingVertical: 8
  },
  linkButton: {
    marginTop: 8
  }
});
```

### 5. Navigation Setup (`src/navigation/AppNavigator.tsx`)

```typescript
import React, { useEffect, useState } from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
import { View, Text, StyleSheet } from 'react-native';
import { ActivityIndicator, Button } from 'react-native-paper';
import Icon from 'react-native-vector-icons/MaterialCommunityIcons';

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

function AuthStack() {
  return (
    <Stack.Navigator 
      initialRouteName="Login"
      screenOptions={{
        headerShown: false
      }}
    >
      <Stack.Screen name="Login" component={LoginScreen} />
      <Stack.Screen name="Register" component={RegisterScreen} />
    </Stack.Navigator>
  );
}

function MainTabs() {
  const [currentUser, setCurrentUser] = useState<any>(null);

  useEffect(() => {
    loadCurrentUser();
  }, []);

  const loadCurrentUser = async () => {
    const user = await authService.getCurrentUser();
    setCurrentUser(user);
  };

  const handleLogout = async () => {
    await authService.logout();
  };

  return (
    <Tab.Navigator
      screenOptions={({ route }) => ({
        tabBarIcon: ({ focused, color, size }) => {
          let iconName;
          
          switch (route.name) {
            case 'Dashboard':
              iconName = focused ? 'view-dashboard' : 'view-dashboard-outline';
              break;
            case 'Appointments':
              iconName = focused ? 'calendar-clock' : 'calendar-clock-outline';
              break;
            case 'Profile':
              iconName = focused ? 'account' : 'account-outline';
              break;
            default:
              iconName = 'circle';
          }
          
          return <Icon name={iconName} size={size} color={color} />;
        },
        tabBarActiveTintColor: '#2E7D32',
        tabBarInactiveTintColor: 'gray',
        headerRight: () => (
          <Button 
            mode="text" 
            onPress={handleLogout}
            style={{ marginRight: 10 }}
          >
            Logout
          </Button>
        ),
        headerTitle: `${route.name} - ${currentUser?.name || 'User'}`
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

  useEffect(() => {
    checkAuthState();
    
    const interval = setInterval(checkAuthState, 1000);
    return () => clearInterval(interval);
  }, []);

  const checkAuthState = async () => {
    try {
      const authenticated = await authService.isAuthenticated();
      console.log('Auth state checked:', authenticated);
      
      if (authenticated !== isAuthenticated) {
        setIsAuthenticated(authenticated);
      }
    } catch (error) {
      console.error('Error checking auth state:', error);
      setIsAuthenticated(false);
    } finally {
      if (isLoading) {
        setIsLoading(false);
      }
    }
  };

  if (isLoading) {
    return <LoadingScreen />;
  }

  return (
    <NavigationContainer>
      {isAuthenticated ? (
        <Stack.Navigator screenOptions={{ headerShown: false }}>
          <Stack.Screen name="Main" component={MainTabs} />
        </Stack.Navigator>
      ) : (
        <AuthStack />
      )}
    </NavigationContainer>
  );
}

const styles = StyleSheet.create({
  loadingContainer: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#F5F7FA'
  },
  loadingText: {
    marginTop: 16,
    fontSize: 16,
    color: '#666'
  }
});
```

### 6. Dashboard Screen (`src/screens/dashboard/DashboardScreen.tsx`)

```typescript
import React, { useEffect, useState, useCallback } from 'react';
import { View, ScrollView, StyleSheet, RefreshControl, Alert } from 'react-native';
import { Card, Title, Paragraph, Button, Text, ActivityIndicator } from 'react-native-paper';
import Icon from 'react-native-vector-icons/MaterialCommunityIcons';
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
          <Icon name={icon} size={24} color={color} />
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

  return (
    <ScrollView 
      style={styles.container}
      refreshControl={
        <RefreshControl refreshing={refreshing} onRefresh={onRefresh} />
      }
    >
      <Card style={styles.welcomeCard}>
        <Card.Content>
          <Title style={styles.welcomeTitle}>
            Welcome, {currentUser?.name || 'User'}!
          </Title>
          <Paragraph style={styles.welcomeSubtitle}>
            Healthcare Provider Dashboard
          </Paragraph>
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
      </View>
    </ScrollView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#F5F7FA',
    padding: 16
  },
  loadingContainer: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#F5F7FA'
  },
  loadingText: {
    marginTop: 16,
    fontSize: 16,
    color: '#666'
  },
  welcomeCard: {
    marginBottom: 20,
    elevation: 2
  },
  welcomeTitle: {
    fontSize: 24,
    fontWeight: 'bold',
    color: '#2E7D32',
    marginBottom: 4
  },
  welcomeSubtitle: {
    fontSize: 16,
    color: '#666'
  },
  sectionTitle: {
    fontSize: 20,
    fontWeight: 'bold',
    marginBottom: 16,
    color: '#333'
  },
  statsGrid: {
    flexDirection: 'row',
    flexWrap: 'wrap',
    justifyContent: 'space-between'
  },
  statCard: {
    width: '48%',
    marginBottom: 12,
    elevation: 2,
    borderLeftWidth: 4
  },
  statContent: {
    paddingVertical: 12
  },
  statHeader: {
    flexDirection: 'row',
    justifyContent: 'space-between',
    alignItems: 'center',
    marginBottom: 8
  },
  statValue: {
    fontSize: 24,
    fontWeight: 'bold'
  },
  statTitle: {
    fontSize: 12,
    color: '#666',
    textAlign: 'center'
  }
});
```

### 7. Appointments Screen (`src/screens/appointments/AppointmentsScreen.tsx`)

```typescript
import React, { useEffect, useState, useCallback } from 'react';
import { View, ScrollView, StyleSheet, RefreshControl, Alert } from 'react-native';
import { Card, Title, Text, Chip, Button, ActivityIndicator } from 'react-native-paper';
import Icon from 'react-native-vector-icons/MaterialCommunityIcons';
import healthcareService from '../../services/healthcareService';

export default function AppointmentsScreen() {
  const [appointments, setAppointments] = useState<any[]>([]);
  const [loading, setLoading] = useState(true);
  const [refreshing, setRefreshing] = useState(false);

  useEffect(() => {
    fetchAppointments();
  }, []);

  const fetchAppointments = async () => {
    try {
      const data = await healthcareService.getAppointments();
      setAppointments(data.appointments || []);
    } catch (error) {
      console