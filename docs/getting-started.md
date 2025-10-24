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
  const [loading, setLoading] = useState(false);

  // استخدام useCallback لمنع إعادة إنشاء الدالة
  const handleInputChange = useCallback((field, value) => {
    setFormData(prev => ({
      ...prev,
      [field]: value
    }));
  }, []);

  const handleBooking = useCallback(async () => {
    // التحقق من الحقول المطلوبة
    const requiredFields = ['name', 'lastname', 'phone', 'date', 'time'];
    const emptyFields = requiredFields.filter(field => !formData[field].trim());
    
    if (emptyFields.length > 0) {
      Alert.alert('خطأ', 'يرجى ملء جميع الحقول المطلوبة');
      return;
    }

    // تحقق من رقم الهاتف
    const phoneRegex = /^[0-9+]{10,}$/;
    if (!phoneRegex.test(formData.phone)) {
      Alert.alert('خطأ', 'يرجى إدخال رقم هاتف صحيح (10 أرقام على الأقل)');
      return;
    }

    // تحقق من صحة التاريخ
    const dateRegex = /^\d{4}-\d{2}-\d{2}$/;
    if (!dateRegex.test(formData.date)) {
      Alert.alert('خطأ', 'يرجى إدخال التاريخ بالصيغة الصحيحة (YYYY-MM-DD)');
      return;
    }

    // تحقق من صحة الوقت
    const timeRegex = /^([01]?[0-9]|2[0-3]):[0-5][0-9]$/;
    if (!timeRegex.test(formData.time)) {
      Alert.alert('خطأ', 'يرجى إدخال الوقت بالصيغة الصحيحة (HH:MM)');
      return;
    }

    setLoading(true);

    try {
      // محاكاة API call - استبدل الرابط برابط API الحقيقي
      await new Promise(resolve => setTimeout(resolve, 1500));
      
      // هنا سيتم استدعاء API الحقيقي
      // const response = await fetch('https://sheet.best/api/sheets/xxxxxx-xxxx-xxxx-xxxx', {
      //   method: 'POST',
      //   headers: { 'Content-Type': 'application/json' },
      //   body: JSON.stringify({
      //     ...formData,
      //     createdAt: new Date().toISOString()
      //   })
      // });

      // if (response.ok) {
        Alert.alert(
          'نجاح', 
          '✅ تم حجز موعدك بنجاح!\n\nسيتم التواصل معك لتأكيد الحجز.',
          [
            {
              text: 'موافق',
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
      // } else {
      //   throw new Error('فشل في الحجز');
      // }
    } catch (error) {
      Alert.alert(
        'خطأ', 
        'حدث خطأ أثناء الحجز. يرجى المحاولة مرة أخرى'
      );
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
        <Text style={styles.header}>حجز موعد مكتب بلمبروك</Text>
        <Text style={styles.subTitle}>اختر موعدك المناسب وادخل معلوماتك</Text>
      </View>
      
      <TouchableOpacity 
        style={styles.primaryButton} 
        onPress={() => setCurrentPage('booking')}
        activeOpacity={0.8}
      >
        <Text style={styles.primaryButtonText}>ابدأ الحجز</Text>
      </TouchableOpacity>

      <View style={styles.infoContainer}>
        <Text style={styles.infoTitle}>معلومات التواصل:</Text>
        <Text style={styles.infoText}>📞 الهاتف: 0123456789</Text>
        <Text style={styles.infoText}>🕒 أوقات العمل: 8:00 ص - 5:00 م</Text>
      </View>
    </View>
  );

  const BookingPage = () => (
    <KeyboardAvoidingView
      style={{ flex: 1 }}
      behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
      keyboardVerticalOffset={Platform.OS === 'ios' ? 60 : 0}
    >
      <ScrollView
        style={styles.container}
        contentContainerStyle={styles.scrollContent}
        showsVerticalScrollIndicator={false}
        keyboardShouldPersistTaps="handled"
      >
        <View style={styles.pageHeader}>
          <Text style={styles.subHeader}>معلومات الحجز</Text>
          <Text style={styles.requiredText}>* الحقول المطلوبة</Text>
        </View>

        <View style={styles.formRow}>
          <View style={styles.halfInput}>
            <Text style={styles.label}>الاسم *</Text>
            <TextInput
              style={[
                styles.input, 
                !formData.name && styles.requiredField
              ]}
              placeholder="أدخل الاسم"
              value={formData.name}
              onChangeText={(value) => handleInputChange('name', value)}
              autoCorrect={false}
              autoCapitalize="words"
              textAlign="right"
            />
          </View>

          <View style={styles.halfInput}>
            <Text style={styles.label}>اللقب *</Text>
            <TextInput
              style={[
                styles.input, 
                !formData.lastname && styles.requiredField
              ]}
              placeholder="أدخل اللقب"
              value={formData.lastname}
              onChangeText={(value) => handleInputChange('lastname', value)}
              autoCorrect={false}
              autoCapitalize="words"
              textAlign="right"
            />
          </View>
        </View>

        <View style={styles.inputGroup}>
          <Text style={styles.label}>رقم الهاتف *</Text>
          <TextInput
            style={[
              styles.input, 
              !formData.phone && styles.requiredField
            ]}
            placeholder="05XXXXXXXX"
            value={formData.phone}
            onChangeText={(value) => handleInputChange('phone', value)}
            keyboardType="phone-pad"
            textAlign="right"
            maxLength={10}
          />
        </View>

        <View style={styles.formRow}>
          <View style={styles.halfInput}>
            <Text style={styles.label}>التاريخ *</Text>
            <TextInput
              style={[
                styles.input, 
                !formData.date && styles.requiredField
              ]}
              placeholder="YYYY-MM-DD"
              value={formData.date}
              onChangeText={(value) => handleInputChange('date', value)}
              textAlign="right"
            />
          </View>

          <View style={styles.halfInput}>
            <Text style={styles.label}>الوقت *</Text>
            <TextInput
              style={[
                styles.input, 
                !formData.time && styles.requiredField
              ]}
              placeholder="HH:MM"
              value={formData.time}
              onChangeText={(value) => handleInputChange('time', value)}
              textAlign="right"
            />
          </View>
        </View>

        <View style={styles.inputGroup}>
          <Text style={styles.label}>ملاحظات إضافية</Text>
          <TextInput
            style={[styles.input, styles.textArea]}
            placeholder="أي ملاحظات أو متطلبات خاصة..."
            value={formData.note}
            onChangeText={(value) => handleInputChange('note', value)}
            multiline
            numberOfLines={4}
            textAlignVertical="top"
            textAlign="right"
          />
        </View>

        <TouchableOpacity 
          style={[
            styles.primaryButton, 
            loading && styles.disabledButton
          ]} 
          onPress={handleBooking}
          disabled={loading}
          activeOpacity={0.8}
        >
          {loading ? (
            <ActivityIndicator color="#fff" size="small" />
          ) : (
            <Text style={styles.primaryButtonText}>تأكيد الحجز</Text>
          )}
        </TouchableOpacity>

        <TouchableOpacity 
          style={styles.secondaryButton} 
          onPress={resetForm}
          disabled={loading}
          activeOpacity={0.8}
        >
          <Text style={styles.secondaryButtonText}>إلغاء والعودة</Text>
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
    padding: 20,
    paddingTop: Platform.OS === 'ios' ? 60 : 40
  },
  scrollContent: {
    paddingBottom: 30
  },
  headerContainer: {
    alignItems: 'center',
    marginBottom: 50,
    marginTop: 20
  },
  headerIcon: {
    fontSize: 50,
    marginBottom: 15
  },
  header: { 
    fontSize: 28, 
    color: '#2c3e50', 
    textAlign: 'center', 
    marginBottom: 10, 
    fontWeight: 'bold',
    fontFamily: Platform.OS === 'ios' ? 'System' : 'Roboto'
  },
  subTitle: {
    fontSize: 16,
    color: '#7f8c8d',
    textAlign: 'center',
    lineHeight: 24
  },
  pageHeader: {
    marginBottom: 30,
    alignItems: 'center'
  },
  subHeader: { 
    fontSize: 24, 
    color: '#2c3e50', 
    textAlign: 'center', 
    marginBottom: 5, 
    fontWeight: 'bold' 
  },
  requiredText: {
    fontSize: 14,
    color: '#e74c3c',
    textAlign: 'center'
  },
  formRow: {
    flexDirection: 'row',
    justifyContent: 'space-between',
    marginBottom: 15
  },
  halfInput: {
    flex: 0.48
  },
  inputGroup: {
    marginBottom: 15
  },
  label: {
    fontSize: 14,
    color: '#2c3e50',
    marginBottom: 8,
    fontWeight: '600',
    textAlign: 'right'
  },
  input: { 
    backgroundColor: 'white', 
    padding: 15, 
    borderRadius: 12, 
    borderWidth: 1, 
    borderColor: '#ddd',
    fontSize: 16,
    textAlign: 'right',
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 1 },
    shadowOpacity: 0.1,
    shadowRadius: 3,
    elevation: 2
  },
  requiredField: {
    borderColor: '#e74c3c',
    backgroundColor: '#fdf2f2'
  },
  textArea: { 
    height: 100,
    textAlignVertical: 'top'
  },
  primaryButton: { 
    backgroundColor: '#3498db', 
    padding: 18, 
    borderRadius: 12, 
    alignItems: 'center', 
    marginTop: 10,
    shadowColor: '#3498db',
    shadowOffset: { width: 0, height: 4 },
    shadowOpacity: 0.3,
    shadowRadius: 8,
    elevation: 5
  },
  primaryButtonText: { 
    color: 'white', 
    fontSize: 18, 
    fontWeight: 'bold' 
  },
  secondaryButton: { 
    backgroundColor: 'transparent', 
    padding: 15, 
    borderRadius: 12, 
    alignItems: 'center', 
    marginTop: 10,
    borderWidth: 1,
    borderColor: '#bdc3c7'
  },
  secondaryButtonText: { 
    color: '#7f8c8d', 
    fontSize: 16 
  },
  disabledButton: {
    backgroundColor: '#bdc3c7',
    shadowOpacity: 0
  },
  infoContainer: {
    marginTop: 40,
    padding: 20,
    backgroundColor: 'white',
    borderRadius: 12,
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 2 },
    shadowOpacity: 0.1,
    shadowRadius: 4,
    elevation: 3
  },
  infoTitle: {
    fontSize: 16,
    fontWeight: 'bold',
    color: '#2c3e50',
    marginBottom: 10,
    textAlign: 'center'
  },
  infoText: {
    fontSize: 14,
    color: '#7f8c8d',
    marginBottom: 5,
    textAlign: 'center'
  }
});

export default App;
