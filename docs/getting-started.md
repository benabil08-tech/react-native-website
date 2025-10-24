import React, { useState } from 'react';
import {
  View,
  Text,
  TextInput,
  TouchableOpacity,
  StyleSheet,
  Alert,
  Platform,
  KeyboardAvoidingView,
  ScrollView
} from 'react-native';

const App = () => {
  const [currentPage, setCurrentPage] = useState('home');
  const [formData, setFormData] = useState({
    name: '',
    lastname: '',
    phone: '',
    date: '',
    time: '',
    note: ''
  });

  const handleInputChange = (field, value) => {
    setFormData(prev => ({
      ...prev,
      [field]: value
    }));
  };

  const handleBooking = async () => {
    if (!formData.name || !formData.lastname || !formData.phone || !formData.date || !formData.time) {
      Alert.alert('خطأ', 'يرجى ملء جميع الحقول المطلوبة');
      return;
    }

    const phoneRegex = /^[0-9+]{10,}$/;
    if (!phoneRegex.test(formData.phone)) {
      Alert.alert('خطأ', 'يرجى إدخال رقم هاتف صحيح');
      return;
    }

    try {
      const response = await fetch('https://sheet.best/api/sheets/xxxxxx-xxxx-xxxx-xxxx', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(formData)
      });

      if (response.ok) {
        Alert.alert('نجاح', '✅ تم حجز موعدك بنجاح!');
        setFormData({ name: '', lastname: '', phone: '', date: '', time: '', note: '' });
        setCurrentPage('home');
      } else {
        throw new Error('فشل في الحجز');
      }
    } catch (error) {
      Alert.alert('خطأ', 'حدث خطأ أثناء الحجز. يرجى المحاولة مرة أخرى');
    }
  };

  const HomePage = () => (
    <View style={styles.container}>
      <Text style={styles.header}>📅 حجز موعد مكتب بلمبروك</Text>
      <TouchableOpacity style={styles.button} onPress={() => setCurrentPage('booking')}>
        <Text style={styles.buttonText}>ابدأ الحجز</Text>
      </TouchableOpacity>
    </View>
  );

  const BookingPage = () => (
    <KeyboardAvoidingView
      style={{ flex: 1 }}
      behavior={Platform.OS === 'ios' ? 'padding' : undefined} // لأندرويد غالبًا لا حاجة للـ "height"
      keyboardVerticalOffset={Platform.OS === 'ios' ? 80 : 0} // لتجنب تغطية iOS
    >
      <ScrollView
        style={styles.container}
        keyboardShouldPersistTaps="handled"
      >
        <Text style={styles.subHeader}>معلومات الحجز</Text>

        <TextInput
          style={styles.input}
          placeholder="الاسم"
          value={formData.name}
          onChangeText={(value) => handleInputChange('name', value)}
          autoCorrect={false}
          autoCapitalize="words"
        />

        <TextInput
          style={styles.input}
          placeholder="اللقب"
          value={formData.lastname}
          onChangeText={(value) => handleInputChange('lastname', value)}
          autoCorrect={false}
          autoCapitalize="words"
        />

        <TextInput
          style={styles.input}
          placeholder="رقم الهاتف"
          value={formData.phone}
          onChangeText={(value) => handleInputChange('phone', value)}
          keyboardType="phone-pad"
        />

        <TextInput
          style={styles.input}
          placeholder="التاريخ (YYYY-MM-DD)"
          value={formData.date}
          onChangeText={(value) => handleInputChange('date', value)}
        />

        <TextInput
          style={styles.input}
          placeholder="الساعة (HH:MM)"
          value={formData.time}
          onChangeText={(value) => handleInputChange('time', value)}
        />

        <TextInput
          style={[styles.input, styles.textArea]}
          placeholder="ملاحظة (اختياري)"
          value={formData.note}
          onChangeText={(value) => handleInputChange('note', value)}
          multiline
          numberOfLines={3}
          textAlignVertical="top"
        />

        <TouchableOpacity style={styles.button} onPress={handleBooking}>
          <Text style={styles.buttonText}>تأكيد الحجز</Text>
        </TouchableOpacity>

        <TouchableOpacity style={styles.backButton} onPress={() => setCurrentPage('home')}>
          <Text style={styles.backButtonText}>رجوع</Text>
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
  app: { flex: 1, backgroundColor: '#f5f5f5' },
  container: { flex: 1, padding: 20, paddingTop: 50 },
  header: { fontSize: 28, color: '#FFD700', textAlign: 'center', marginBottom: 40, fontWeight: 'bold' },
  subHeader: { fontSize: 24, color: '#333', textAlign: 'center', marginBottom: 30, fontWeight: 'bold' },
  input: { backgroundColor: 'white', padding: 15, marginBottom: 15, borderRadius: 10, borderWidth: 1, borderColor: '#ddd', textAlign: 'right' },
  textArea: { height: 80 },
  button: { backgroundColor: '#FFD700', padding: 15, borderRadius: 10, alignItems: 'center', marginTop: 10 },
  buttonText: { color: '#333', fontSize: 18, fontWeight: 'bold' },
  backButton: { backgroundColor: '#666', padding: 15, borderRadius: 10, alignItems: 'center', marginTop: 10 },
  backButtonText: { color: 'white', fontSize: 16 }
});

export default App;
