import React, { useState, useCallback } from 'react';
import {
  View,
  Text,
  TextInput,
  TouchableOpacity,
  StyleSheet,
  Alert,
  Platform,
  KeyboardAvoidingView,
  ScrollView,
  ActivityIndicator
} from 'react-native';

const AppointmentBookingApp = () => {
  const [currentPage, setCurrentPage] = useState('home');
  const [formData, setFormData] = useState({
    name: '',
    lastname: '',
    phone: '',
    date: '',
    time: '',
    note: ''
  });
  const [loading, setLoading] = useState(false);

  const handleInputChange = useCallback((field, value) => {
    setFormData(prev => ({
      ...prev,
      [field]: value
    }));
  }, []);

  const handleBooking = useCallback(async () => {
    const requiredFields = ['name', 'lastname', 'phone', 'date', 'time'];
    const emptyFields = requiredFields.filter(field => !formData[field].trim());
    
    if (emptyFields.length > 0) {
      Alert.alert('Error', 'Please fill all required fields');
      return;
    }

    const phoneRegex = /^[0-9+]{10,}$/;
    if (!phoneRegex.test(formData.phone)) {
      Alert.alert('Error', 'Please enter a valid phone number');
      return;
    }

    setLoading(true);

    try {
      // Simulate API call
      await new Promise(resolve => setTimeout(resolve, 1500));
      
      Alert.alert(
        'Success', 
        '✅ Appointment booked successfully!',
        [
          {
            text: 'OK',
            onPress: () => {
              setFormData({ 
                name: '', 
                lastname: '', 
                phone: '', 
                date: '', 
                time: '', 
                note: '' 
              });
              setCurrentPage('home');
            }
          }
        ]
      );
    } catch (error) {
      Alert.alert('Error', 'Booking failed. Please try again.');
    } finally {
      setLoading(false);
    }
  }, [formData]);

  const resetForm = useCallback(() => {
    setFormData({ 
      name: '', 
      lastname: '', 
      phone: '', 
      date: '', 
      time: '', 
      note: '' 
    });
    setCurrentPage('home');
  }, []);

  const HomePage = () => (
    <View style={styles.container}>
      <View style={styles.headerContainer}>
        <Text style={styles.headerIcon}>📅</Text>
        <Text style={styles.header}>Appointment Booking</Text>
        <Text style={styles.subTitle}>Book your appointment easily</Text>
      </View>
      
      <TouchableOpacity 
        style={styles.primaryButton} 
        onPress={() => setCurrentPage('booking')}
        activeOpacity={0.8}
      >
        <Text style={styles.primaryButtonText}>Start Booking</Text>
      </TouchableOpacity>
    </View>
  );

  const BookingPage = () => (
    <KeyboardAvoidingView
      style={{ flex: 1 }}
      behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
    >
      <ScrollView
        style={styles.container}
        contentContainerStyle={styles.scrollContent}
        showsVerticalScrollIndicator={false}
      >
        <View style={styles.pageHeader}>
          <Text style={styles.subHeader}>Booking Information</Text>
          <Text style={styles.requiredText}>* Required fields</Text>
        </View>

        <View style={styles.inputGroup}>
          <Text style={styles.label}>First Name *</Text>
          <TextInput
            style={[
              styles.input, 
              !formData.name && styles.requiredField
            ]}
            placeholder="Enter first name"
            value={formData.name}
            onChangeText={(value) => handleInputChange('name', value)}
            autoCorrect={false}
          />
        </View>

        <View style={styles.inputGroup}>
          <Text style={styles.label}>Last Name *</Text>
          <TextInput
            style={[
              styles.input, 
              !formData.lastname && styles.requiredField
            ]}
            placeholder="Enter last name"
            value={formData.lastname}
            onChangeText={(value) => handleInputChange('lastname', value)}
            autoCorrect={false}
          />
        </View>

        <View style={styles.inputGroup}>
          <Text style={styles.label}>Phone Number *</Text>
          <TextInput
            style={[
              styles.input, 
              !formData.phone && styles.requiredField
            ]}
            placeholder="Enter phone number"
            value={formData.phone}
            onChangeText={(value) => handleInputChange('phone', value)}
            keyboardType="phone-pad"
          />
        </View>

        <View style={styles.formRow}>
          <View style={styles.halfInput}>
            <Text style={styles.label}>Date *</Text>
            <TextInput
              style={[
                styles.input, 
                !formData.date && styles.requiredField
              ]}
              placeholder="YYYY-MM-DD"
              value={formData.date}
              onChangeText={(value) => handleInputChange('date', value)}
            />
          </View>

          <View style={styles.halfInput}>
            <Text style={styles.label}>Time *</Text>
            <TextInput
              style={[
                styles.input, 
                !formData.time && styles.requiredField
              ]}
              placeholder="HH:MM"
              value={formData.time}
              onChangeText={(value) => handleInputChange('time', value)}
            />
          </View>
        </View>

        <View style={styles.inputGroup}>
          <Text style={styles.label}>Notes</Text>
          <TextInput
            style={[styles.input, styles.textArea]}
            placeholder="Additional notes..."
            value={formData.note}
            onChangeText={(value) => handleInputChange('note', value)}
            multiline
            numberOfLines={4}
            textAlignVertical="top"
          />
        </View>

        <TouchableOpacity 
          style={[
            styles.primaryButton, 
            loading && styles.disabledButton
          ]} 
          onPress={handleBooking}
          disabled={loading}
        >
          {loading ? (
            <ActivityIndicator color="#fff" />
          ) : (
            <Text style={styles.primaryButtonText}>Confirm Booking</Text>
          )}
        </TouchableOpacity>

        <TouchableOpacity 
          style={styles.secondaryButton} 
          onPress={resetForm}
          disabled={loading}
        >
          <Text style={styles.secondaryButtonText}>Cancel</Text>
        </TouchableOpacity>
      </ScrollView>
    </KeyboardAvoidingView>
  );

  return (
    <View style={styles.app}>
      {currentPage === 'home' ? <HomePage /> : <BookingPage />}
    </View>
  );
};

const styles = StyleSheet.create({
  app: { 
    flex: 1, 
    backgroundColor: '#f8f9fa' 
  },
  container: { 
    flex: 1, 
    padding: 16,
  },
  scrollContent: {
    paddingBottom: 20
  },
  headerContainer: {
    alignItems: 'center',
    marginBottom: 40,
    marginTop: 20
  },
  headerIcon: {
    fontSize: 40,
    marginBottom: 10
  },
  header: { 
    fontSize: 24, 
    color: '#2c3e50', 
    textAlign: 'center', 
    marginBottom: 8, 
    fontWeight: 'bold'
  },
  subTitle: {
    fontSize: 14,
    color: '#7f8c8d',
    textAlign: 'center',
  },
  pageHeader: {
    marginBottom: 20,
  },
  subHeader: { 
    fontSize: 20, 
    color: '#2c3e50', 
    marginBottom: 4, 
    fontWeight: 'bold' 
  },
  requiredText: {
    fontSize: 12,
    color: '#e74c3c',
  },
  formRow: {
    flexDirection: 'row',
    justifyContent: 'space-between',
    marginBottom: 12
  },
  halfInput: {
    flex: 0.48
  },
  inputGroup: {
    marginBottom: 12
  },
  label: {
    fontSize: 14,
    color: '#2c3e50',
    marginBottom: 6,
    fontWeight: '600',
  },
  input: { 
    backgroundColor: 'white', 
    padding: 12, 
    borderRadius: 8, 
    borderWidth: 1, 
    borderColor: '#ddd',
    fontSize: 16,
  },
  requiredField: {
    borderColor: '#e74c3c',
  },
  textArea: { 
    height: 80,
  },
  primaryButton: { 
    backgroundColor: '#007AFF', 
    padding: 16, 
    borderRadius: 8, 
    alignItems: 'center', 
    marginTop: 8,
  },
  primaryButtonText: { 
    color: 'white', 
    fontSize: 16, 
    fontWeight: 'bold' 
  },
  secondaryButton: { 
    backgroundColor: 'transparent', 
    padding: 12, 
    borderRadius: 8, 
    alignItems: 'center', 
    marginTop: 8,
    borderWidth: 1,
    borderColor: '#bdc3c7'
  },
  secondaryButtonText: { 
    color: '#7f8c8d', 
    fontSize: 14 
  },
  disabledButton: {
    backgroundColor: '#bdc3c7',
  },
});

export default AppointmentBookingApp;
