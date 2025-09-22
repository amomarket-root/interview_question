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
  ? 'http://10.0.2.2:8000/api'  // Android Emulator
  : 'https://your-production-domain.com/api';

const apiClient = axios.create({
  baseURL: API_BASE_URL,
  timeout: 30000,
  headers: {
    'Content-Type': 'application/json',
    'Accept': 'application/json',
  },
});

// Request interceptor for auth tokens
apiClient.interceptors.request.use(async (config) => {
  const token = await AsyncStorage.getItem('auth_token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

// Response interceptor for error handling
apiClient.interceptors.response.use(
  (response) => response,
  async (error) => {
    if (error.response?.status === 401) {
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
        [{ text: 'OK', onPress: () => navigation.navigate('Dashboard') }]
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

  const handleTestLogin = () => {
    // Pre-fill with test credentials for healthcare user
    Alert.alert(
      'Test Login',
      'Would you like to use test credentials?',
      [
        { text: 'Cancel', style: 'cancel' },
        { 
          text: 'Use Test Healthcare', 
          onPress: () => {
            // You can update these with actual test credentials from your database
            onLogin({ 
              email: 'healthcare@test.com', 
              password: 'password123' 
            });
          }
        }
      ]
    );
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
                mode="outlined"
                onPress={handleTestLogin}
                disabled={loading}
                style={styles.testButton}
              >
                Use Test Credentials
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
  testButton: {
    marginBottom: 16
  },
  linkButton: {
    marginTop: 8
  }
});
```
### 5. Register Screen (`src/screens/auth/RegisterScreen.tsx`)

```typescript
import React, { useState } from 'react';
import { View, StyleSheet, Alert, ScrollView, KeyboardAvoidingView, Platform } from 'react-native';
import { TextInput, Button, Card, Title, Text, HelperText, RadioButton } from 'react-native-paper';
import { useForm, Controller } from 'react-hook-form';
import authService, { RegisterData } from '../../services/authService';

interface RegisterScreenProps {
  navigation: any;
}

export default function RegisterScreen({ navigation }: RegisterScreenProps) {
  const [loading, setLoading] = useState(false);
  const [showPassword, setShowPassword] = useState(false);
  const [showConfirmPassword, setShowConfirmPassword] = useState(false);
  
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
        [{ text: 'OK', onPress: () => navigation.replace('Main') }]
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
                        <RadioButton value="patient" />
                        <Text style={styles.radioLabel}>Patient</Text>
                      </View>
                      <View style={styles.radioItem}>
                        <RadioButton value="doctor" />
                        <Text style={styles.radioLabel}>Doctor</Text>
                      </View>
                      <View style={styles.radioItem}>
                        <RadioButton value="healthcare" />
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
              >
                {loading ? 'Creating Account...' : 'Register'}
              </Button>
              
              <Button
                mode="text"
                onPress={() => navigation.navigate('Login')}
                disabled={loading}
                style={styles.linkButton}
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

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#F5F7FA'
  },
  scrollContent: {
    flexGrow: 1,
    paddingVertical: 20
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
  sectionTitle: {
    fontSize: 16,
    fontWeight: 'bold',
    marginTop: 16,
    marginBottom: 12,
    color: '#333'
  },
  radioGroup: {
    marginBottom: 16
  },
  radioItem: {
    flexDirection: 'row',
    alignItems: 'center',
    marginBottom: 8
  },
  radioLabel: {
    marginLeft: 8,
    fontSize: 16
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

### 6. Navigation Setup (`src/navigation/AppNavigator.tsx`)

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
    // Navigation will be handled by the auth state change
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
  }, []);

  const checkAuthState = async () => {
    try {
      const authenticated = await authService.isAuthenticated();
      console.log('Auth state checked:', authenticated);
      setIsAuthenticated(authenticated);
    } catch (error) {
      console.error('Error checking auth state:', error);
      setIsAuthenticated(false);
    } finally {
      setIsLoading(false);
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

### 7. Dashboard Screen (`src/screens/dashboard/DashboardScreen.tsx`)

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
        <RefreshControl refreshing={refreshing} onRefresh={onRefresh} />
      }
    >
      {/* Welcome Header */}
      <Card style={styles.welcomeCard}>
        <Card.Content>
          <Title style={styles.welcomeTitle}>
            Welcome, {currentUser?.name || 'User'}!
          </Title>
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

      {/* Quick Stats */}
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

      {/* Quick Actions */}
      <Text style={styles.sectionTitle}>Quick Actions</Text>
      
      <View style={styles.actionsGrid}>
        <Card style={styles.actionCard}>
          <Card.Content style={styles.actionContent}>
            <Icon name="calendar-plus" size={32} color="#2E7D32" />
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
            <Icon name="account-cog" size={32} color="#2E7D32" />
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
            <Icon name="check-circle" size={20} color="#4CAF50" />
            <Text style={styles.activityText}>Dashboard loaded successfully</Text>
          </View>
          <View style={styles.activityItem}>
            <Icon name="update" size={20} color="#FF9800" />
            <Text style={styles.activityText}>Data last updated: {new Date().toLocaleTimeString()}</Text>
          </View>
        </Card.Content>
      </Card>
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
    color: '#666',
    marginBottom: 8
  },
  welcomeDate: {
    fontSize: 14,
    color: '#999'
  },
  sectionTitle: {
    fontSize: 20,
    fontWeight: 'bold',
    marginBottom: 16,
    marginTop: 8,
    color: '#333'
  },
  statsGrid: {
    flexDirection: 'row',
    flexWrap: 'wrap',
    justifyContent: 'space-between',
    marginBottom: 24
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
  },
  actionsGrid: {
    flexDirection: 'row',
    justifyContent: 'space-between',
    marginBottom: 24
  },
  actionCard: {
    width: '48%',
    elevation: 2
  },
  actionContent: {
    alignItems: 'center',
    paddingVertical: 20
  },
  actionButton: {
    marginTop: 12,
    width: '100%'
  },
  activityCard: {
    elevation: 2,
    marginBottom: 20
  },
  activityItem: {
    flexDirection: 'row',
    alignItems: 'center',
    paddingVertical: 8
  },
  activityText: {
    marginLeft: 12,
    fontSize: 14,
    color: '#666'
  }
});
```

### 8. Appointments Screen (`src/screens/appointments/AppointmentsScreen.tsx`)

```typescript
import React, { useEffect, useState, useCallback } from 'react';
import { View, ScrollView, StyleSheet, RefreshControl, Alert } from 'react-native';
import { Card, Title, Text, Chip, Button, ActivityIndicator } from 'react-native-paper';
import Icon from 'react-native-vector-icons/MaterialCommunityIcons';
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
            <Icon name="calendar-blank" size={64} color="#CCC" />
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
                    <Icon 
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
                  <Icon name="calendar" size={16} color="#666" />
                  <Text style={styles.detailText}>
                    {new Date(appointment.appointment_date).toLocaleDateString()}
                  </Text>
                </View>
                
                <View style={styles.detailItem}>
                  <Icon name="clock" size={16} color="#666" />
                  <Text style={styles.detailText}>
                    {appointment.appointment_time}
                  </Text>
                </View>
                
                <View style={styles.detailItem}>
                  <Icon name="medical-bag" size={16} color="#666" />
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
  title: {
    textAlign: 'center',
    marginBottom: 20,
    color: '#2E7D32',
    fontSize: 24
  },
  emptyCard: {
    elevation: 2,
    marginTop: 50
  },
  emptyContent: {
    alignItems: 'center',
    paddingVertical: 40
  },
  emptyText: {
    fontSize: 18,
    color: '#666',
    marginVertical: 20
  },
  retryButton: {
    marginTop: 10
  },
  appointmentCard: {
    marginBottom: 16,
    elevation: 2
  },
  appointmentHeader: {
    flexDirection: 'row',
    justifyContent: 'space-between',
    alignItems: 'flex-start',
    marginBottom: 16
  },
  appointmentInfo: {
    flex: 1
  },
  patientName: {
    fontSize: 18,
    fontWeight: 'bold',
    color: '#333',
    marginBottom: 4
  },
  doctorName: {
    fontSize: 14,
    color: '#666'
  },
  statusChip: {
    marginLeft: 8
  },
  chipText: {
    color: 'white',
    fontSize: 12,
    fontWeight: 'bold'
  },
  appointmentDetails: {
    marginBottom: 16
  },
  detailItem: {
    flexDirection: 'row',
    alignItems: 'center',
    marginBottom: 8
  },
  detailText: {
    marginLeft: 8,
    fontSize: 14,
    color: '#666'
  },
  notesSection: {
    backgroundColor: '#F0F0F0',
    padding: 12,
    borderRadius: 8,
    marginBottom: 16
  },
  notesLabel: {
    fontSize: 12,
    fontWeight: 'bold',
    color: '#333',
    marginBottom: 4
  },
  notesText: {
    fontSize: 14,
    color: '#666'
  },
  actionButtons: {
    flexDirection: 'row',
    justifyContent: 'space-between'
  },
  actionButton: {
    flex: 1,
    marginHorizontal: 4
  }
});
```

### 9. Appointments Screen (`src/screens/profile/ProfileScreen.tsx`)

```typescript
import React, { useEffect, useState } from 'react';
import { View, ScrollView, StyleSheet, Alert } from 'react-native';
import { Card, Title, Text, Button, Avatar, Divider } from 'react-native-paper';
import Icon from 'react-native-vector-icons/MaterialCommunityIcons';
import authService from '../../services/authService';
import healthcareService from '../../services/healthcareService';

export default function ProfileScreen({ navigation }: any) {
  const [user, setUser] = useState<any>(null);
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    loadUserProfile();
  }, []);

  const loadUserProfile = async () => {
    try {
      const currentUser = await authService.getCurrentUser();
      setUser(currentUser);
      
      // Try to get more detailed profile from API
      const profileData = await healthcareService.getProfile();
      if (profileData?.user) {
        setUser(profileData.user);
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

  if (!user) {
    return (
      <View style={styles.loadingContainer}>
        <Text>Loading profile...</Text>
      </View>
    );
  }

  return (
    <ScrollView style={styles.container}>
      {/* Profile Header */}
      <Card style={styles.profileCard}>
        <Card.Content style={styles.profileContent}>
          <Avatar.Text 
            size={80} 
            label={user.name?.charAt(0)?.toUpperCase() || 'U'} 
            style={styles.avatar}
          />
          <Title style={styles.userName}>{user.name || 'User'}</Title>
          <Text style={styles.userType}>{getUserTypeDisplay(user.user_type)}</Text>
          <Text style={styles.userEmail}>{user.email}</Text>
        </Card.Content>
      </Card>

      {/* Profile Details */}
      <Card style={styles.detailsCard}>
        <Card.Content>
          <Title style={styles.sectionTitle}>Account Information</Title>
          
          <View style={styles.detailRow}>
            <Icon name="account" size={20} color="#666" />
            <View style={styles.detailContent}>
              <Text style={styles.detailLabel}>Full Name</Text>
              <Text style={styles.detailValue}>{user.name || 'Not provided'}</Text>
            </View>
          </View>

          <Divider style={styles.divider} />

          <View style={styles.detailRow}>
            <Icon name="email" size={20} color="#666" />
            <View style={styles.detailContent}>
              <Text style={styles.detailLabel}>Email Address</Text>
              <Text style={styles.detailValue}>{user.email}</Text>
            </View>
          </View>

          <Divider style={styles.divider} />

          <View style={styles.detailRow}>
            <Icon name="phone" size={20} color="#666" />
            <View style={styles.detailContent}>
              <Text style={styles.detailLabel}>Phone Number</Text>
              <Text style={styles.detailValue}>{user.phone || 'Not provided'}</Text>
            </View>
          </View>

          <Divider style={styles.divider} />

          <View style={styles.detailRow}>
            <Icon name="badge-account" size={20} color="#666" />
            <View style={styles.detailContent}>
              <Text style={styles.detailLabel}>User Type</Text>
              <Text style={styles.detailValue}>{getUserTypeDisplay(user.user_type)}</Text>
            </View>
          </View>

          {user.address && (
            <>
              <Divider style={styles.divider} />
              <View style={styles.detailRow}>
                <Icon name="map-marker" size={20} color="#666" />
                <View style={styles.detailContent}>
                  <Text style={styles.detailLabel}>Address</Text>
                  <Text style={styles.detailValue}>{user.address}</Text>
                </View>
              </View>
            </>
          )}
        </Card.Content>
      </Card>

      {/* Action Buttons */}
      <Card style={styles.actionsCard}>
        <Card.Content>
          <Title style={styles.sectionTitle}>Account Actions</Title>
          
          <Button
            mode="outlined"
            icon="account-edit"
            onPress={() => Alert.alert('Info', 'Edit profile functionality')}
            style={styles.actionButton}
          >
            Edit Profile
          </Button>

          <Button
            mode="outlined"
            icon="lock-reset"
            onPress={() => Alert.alert('Info', 'Change password functionality')}
            style={styles.actionButton}
          >
            Change Password
          </Button>

          <Button
            mode="outlined"
            icon="bell-cog"
            onPress={() => Alert.alert('Info', 'Notification settings functionality')}
            style={styles.actionButton}
          >
            Notification Settings
          </Button>

          <Button
            mode="contained"
            icon="logout"
            onPress={handleLogout}
            loading={loading}
            style={[styles.actionButton, styles.logoutButton]}
            buttonColor="#F44336"
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
        </Card.Content>
      </Card>
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
    alignItems: 'center'
  },
  profileCard: {
    marginBottom: 16,
    elevation: 2
  },
  profileContent: {
    alignItems: 'center',
    paddingVertical: 20
  },
  avatar: {
    backgroundColor: '#2E7D32',
    marginBottom: 16
  },
  userName: {
    fontSize: 24,
    fontWeight: 'bold',
    color: '#333',
    marginBottom: 4
  },
  userType: {
    fontSize: 16,
    color: '#2E7D32',
    marginBottom: 4,
    fontWeight: '500'
  },
  userEmail: {
    fontSize: 14,
    color: '#666'
  },
  detailsCard: {
    marginBottom: 16,
    elevation: 2
  },
  sectionTitle: {
    fontSize: 18,
    marginBottom: 16,
    color: '#333'
  },
  detailRow: {
    flexDirection: 'row',
    alignItems: 'center',
    paddingVertical: 12
  },
  detailContent: {
    marginLeft: 16,
    flex: 1
  },
  detailLabel: {
    fontSize: 12,
    color: '#666',
    marginBottom: 2,
    textTransform: 'uppercase'
  },
  detailValue: {
    fontSize: 16,
    color: '#333'
  },
  divider: {
    marginVertical: 4
  },
  actionsCard: {
    marginBottom: 16,
    elevation: 2
  },
  actionButton: {
    marginBottom: 12
  },
  logoutButton: {
    marginTop: 8
  },
  infoCard: {
    elevation: 1,
    marginBottom: 20
  },
  appInfo: {
    textAlign: 'center',
    fontSize: 12,
    color: '#999',
    marginBottom: 4
  }
});
```

### 10. App.tsx 

```typescript
iimport React from 'react';
import { PaperProvider, DefaultTheme } from 'react-native-paper';
import { StatusBar } from 'expo-status-bar';
import AppNavigator from './src/navigation/AppNavigator';

const theme = {
  ...DefaultTheme,
  colors: {
    ...DefaultTheme.colors,
    primary: '#2E7D32',
    accent: '#4CAF50',
  },
};

export default function App() {
  return (
    <PaperProvider theme={theme}>
      <StatusBar style="auto" />
      <AppNavigator />
    </PaperProvider>
  );
}
```
