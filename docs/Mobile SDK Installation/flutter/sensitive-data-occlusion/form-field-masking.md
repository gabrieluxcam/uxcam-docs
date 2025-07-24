---
title: Form Field Masking
excerpt: Specialized privacy protection for Flutter form inputs and user data entry
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---

# Form Field Masking

Implement comprehensive privacy protection for Flutter form inputs, ensuring sensitive user data is properly masked during data entry while maintaining form functionality and user experience.

## Overview

Form field masking in Flutter requires special consideration for:
- **Input State Management**: Handling focus, validation, and user interaction
- **Text Field Variants**: TextFormField, TextField, custom inputs, and dropdowns
- **Dynamic Masking**: Real-time occlusion based on field type and content
- **User Experience**: Maintaining usability while protecting privacy
- **Validation Integration**: Working with form validation without exposing data

## Basic Form Field Occlusion

### Secure Text Field Wrapper
```dart
import 'package:flutter_uxcam/flutter_uxcam.dart';

class SecureTextField extends StatefulWidget {
  final TextEditingController? controller;
  final String? labelText;
  final String? hintText;
  final bool obscureText;
  final TextInputType? keyboardType;
  final String? Function(String?)? validator;
  final void Function(String)? onChanged;
  final bool autoOcclude;
  final String occlusionReason;
  
  const SecureTextField({
    Key? key,
    this.controller,
    this.labelText,
    this.hintText,
    this.obscureText = false,
    this.keyboardType,
    this.validator,
    this.onChanged,
    this.autoOcclude = true,
    this.occlusionReason = 'secure_text_field',
  }) : super(key: key);
  
  @override
  _SecureTextFieldState createState() => _SecureTextFieldState();
}

class _SecureTextFieldState extends State<SecureTextField> {
  final GlobalKey _fieldKey = GlobalKey();
  late FocusNode _focusNode;
  bool _isFocused = false;
  
  @override
  void initState() {
    super.initState();
    _focusNode = FocusNode();
    _focusNode.addListener(_handleFocusChange);
    
    // Apply initial occlusion
    WidgetsBinding.instance.addPostFrameCallback((_) {
      _updateOcclusion();
    });
  }
  
  void _handleFocusChange() {
    setState(() {
      _isFocused = _focusNode.hasFocus;
    });
    
    // Update occlusion based on focus state
    _updateOcclusion();
    
    // Log focus events for sensitive fields
    if (widget.autoOcclude) {
      FlutterUxcam.logEvent('secure_field_focus_changed', {
        'field_label': widget.labelText ?? 'unnamed_field',
        'focused': _isFocused,
        'occlusion_reason': widget.occlusionReason,
        'timestamp': DateTime.now().toIso8601String(),
      });
    }
  }
  
  void _updateOcclusion() {
    WidgetsBinding.instance.addPostFrameCallback((_) {
      if (widget.autoOcclude && mounted) {
        _applyFieldOcclusion();
      }
    });
  }
  
  void _applyFieldOcclusion() {
    final renderBox = _fieldKey.currentContext?.findRenderObject() as RenderBox?;
    if (renderBox != null && renderBox.hasSize) {
      final position = renderBox.localToGlobal(Offset.zero);
      final size = renderBox.size;
      
      FlutterUxcam.occludeRect(
        position.dx,
        position.dy,
        size.width,
        size.height,
      );
    }
  }
  
  @override
  Widget build(BuildContext context) {
    return Container(
      key: _fieldKey,
      child: TextFormField(
        controller: widget.controller,
        focusNode: _focusNode,
        obscureText: widget.obscureText,
        keyboardType: widget.keyboardType,
        validator: widget.validator,
        onChanged: widget.onChanged,
        decoration: InputDecoration(
          labelText: widget.labelText,
          hintText: widget.hintText,
          border: OutlineInputBorder(),
          // Add visual indicator for secure fields
          suffixIcon: widget.autoOcclude 
              ? Icon(Icons.security, size: 16, color: Colors.grey)
              : null,
        ),
      ),
    );
  }
  
  @override
  void dispose() {
    _focusNode.removeListener(_handleFocusChange);
    _focusNode.dispose();
    super.dispose();
  }
}
```

### Smart Field Type Detection
```dart
class SmartSecureField extends StatelessWidget {
  final TextEditingController? controller;
  final String? labelText;
  final String? hintText;
  final TextInputType? keyboardType;
  final String? Function(String?)? validator;
  final void Function(String)? onChanged;
  
  const SmartSecureField({
    Key? key,
    this.controller,
    this.labelText,
    this.hintText,
    this.keyboardType,
    this.validator,
    this.onChanged,
  }) : super(key: key);
  
  @override
  Widget build(BuildContext context) {
    final fieldInfo = _analyzeFieldType();
    
    return SecureTextField(
      controller: controller,
      labelText: labelText,
      hintText: hintText,
      keyboardType: keyboardType,
      validator: validator,
      onChanged: onChanged,
      obscureText: fieldInfo.shouldObscure,
      autoOcclude: fieldInfo.shouldOcclude,
      occlusionReason: fieldInfo.occlusionReason,
    );
  }
  
  FieldAnalysis _analyzeFieldType() {
    final label = (labelText ?? '').toLowerCase();
    final hint = (hintText ?? '').toLowerCase();
    final combinedText = '$label $hint';
    
    // Password fields
    if (combinedText.contains('password') || 
        combinedText.contains('passcode') ||
        combinedText.contains('pin')) {
      return FieldAnalysis(
        shouldObscure: true,
        shouldOcclude: true,
        occlusionReason: 'password_field',
      );
    }
    
    // Credit card information
    if (combinedText.contains('card') ||
        combinedText.contains('credit') ||
        combinedText.contains('cvv') ||
        combinedText.contains('cvc') ||
        keyboardType == TextInputType.number && 
        (combinedText.contains('number') || combinedText.contains('expiry'))) {
      return FieldAnalysis(
        shouldObscure: false,
        shouldOcclude: true,
        occlusionReason: 'payment_info',
      );
    }
    
    // Personal identification
    if (combinedText.contains('ssn') ||
        combinedText.contains('social') ||
        combinedText.contains('license') ||
        combinedText.contains('passport')) {
      return FieldAnalysis(
        shouldObscure: false,
        shouldOcclude: true,
        occlusionReason: 'personal_id',
      );
    }
    
    // Email (might be considered sensitive)
    if (keyboardType == TextInputType.emailAddress ||
        combinedText.contains('email')) {
      return FieldAnalysis(
        shouldObscure: false,
        shouldOcclude: _shouldOccludeEmail(),
        occlusionReason: 'email_address',
      );
    }
    
    // Phone numbers
    if (keyboardType == TextInputType.phone ||
        combinedText.contains('phone') ||
        combinedText.contains('mobile')) {
      return FieldAnalysis(
        shouldObscure: false,
        shouldOcclude: true,
        occlusionReason: 'phone_number',
      );
    }
    
    // Default - not sensitive
    return FieldAnalysis(
      shouldObscure: false,
      shouldOcclude: false,
      occlusionReason: 'regular_field',
    );
  }
  
  bool _shouldOccludeEmail() {
    // Check user preferences or app settings
    // For this example, we'll default to not occluding email
    return false;
  }
}

class FieldAnalysis {
  final bool shouldObscure;
  final bool shouldOcclude;
  final String occlusionReason;
  
  FieldAnalysis({
    required this.shouldObscure,
    required this.shouldOcclude,
    required this.occlusionReason,
  });
}
```

## Advanced Form Patterns

### Complete Secure Form
```dart
class SecureFormWidget extends StatefulWidget {
  final String formTitle;
  final List<FormFieldConfig> fields;
  final void Function(Map<String, String>) onSubmit;
  final bool enableOcclusion;
  
  const SecureFormWidget({
    Key? key,
    required this.formTitle,
    required this.fields,
    required this.onSubmit,
    this.enableOcclusion = true,
  }) : super(key: key);
  
  @override
  _SecureFormWidgetState createState() => _SecureFormWidgetState();
}

class _SecureFormWidgetState extends State<SecureFormWidget> {
  final _formKey = GlobalKey<FormState>();
  final Map<String, TextEditingController> _controllers = {};
  final Map<String, bool> _fieldOcclusionStates = {};
  
  @override
  void initState() {
    super.initState();
    
    // Initialize controllers and occlusion states
    for (final field in widget.fields) {
      _controllers[field.key] = TextEditingController();
      _fieldOcclusionStates[field.key] = field.isSecure && widget.enableOcclusion;
    }
    
    // Log form initialization
    FlutterUxcam.logEvent('secure_form_initialized', {
      'form_title': widget.formTitle,
      'field_count': widget.fields.length,
      'secure_field_count': widget.fields.where((f) => f.isSecure).length,
      'occlusion_enabled': widget.enableOcclusion,
    });
  }
  
  @override
  Widget build(BuildContext context) {
    return Form(
      key: _formKey,
      child: Column(
        crossAxisAlignment: CrossAxisAlignment.stretch,
        children: [
          // Form title
          Padding(
            padding: EdgeInsets.all(16),
            child: Text(
              widget.formTitle,
              style: Theme.of(context).textTheme.headline5,
            ),
          ),
          
          // Form fields
          ...widget.fields.map((field) => _buildFormField(field)),
          
          // Submit button
          Padding(
            padding: EdgeInsets.all(16),
            child: ElevatedButton(
              onPressed: _handleSubmit,
              child: Text('Submit'),
            ),
          ),
          
          // Privacy toggle
          if (widget.enableOcclusion) _buildPrivacyToggle(),
        ],
      ),
    );
  }
  
  Widget _buildFormField(FormFieldConfig field) {
    return Padding(
      padding: EdgeInsets.symmetric(horizontal: 16, vertical: 8),
      child: SecureTextField(
        controller: _controllers[field.key],
        labelText: field.label,
        hintText: field.hint,
        keyboardType: field.keyboardType,
        obscureText: field.obscureText,
        autoOcclude: _fieldOcclusionStates[field.key] ?? false,
        occlusionReason: field.occlusionReason,
        validator: (value) => field.validator?.call(value) ?? _defaultValidator(value, field),
        onChanged: (value) => _handleFieldChange(field.key, value),
      ),
    );
  }
  
  Widget _buildPrivacyToggle() {
    return Padding(
      padding: EdgeInsets.all(16),
      child: Row(
        children: [
          Icon(Icons.security, size: 20),
          SizedBox(width: 8),
          Expanded(
            child: Text(
              'Hide sensitive data in recordings',
              style: TextStyle(fontSize: 14),
            ),
          ),
          Switch(
            value: widget.enableOcclusion,
            onChanged: _toggleGlobalOcclusion,
          ),
        ],
      ),
    );
  }
  
  void _handleFieldChange(String fieldKey, String value) {
    // Log field interaction (without the actual value)
    final field = widget.fields.firstWhere((f) => f.key == fieldKey);
    
    FlutterUxcam.logEvent('form_field_interaction', {
      'form_title': widget.formTitle,
      'field_key': fieldKey,
      'field_type': field.occlusionReason,
      'is_secure': field.isSecure,
      'has_value': value.isNotEmpty,
      'value_length': value.length,
    });
  }
  
  void _toggleGlobalOcclusion(bool enabled) {
    setState(() {
      for (final field in widget.fields) {
        if (field.isSecure) {
          _fieldOcclusionStates[field.key] = enabled;
        }
      }
    });
    
    FlutterUxcam.logEvent('form_occlusion_toggled', {
      'form_title': widget.formTitle,
      'occlusion_enabled': enabled,
      'affected_fields': widget.fields.where((f) => f.isSecure).length,
    });
  }
  
  String? _defaultValidator(String? value, FormFieldConfig field) {
    if (field.required && (value == null || value.isEmpty)) {
      return '${field.label} is required';
    }
    return null;
  }
  
  void _handleSubmit() {
    if (_formKey.currentState?.validate() ?? false) {
      final formData = <String, String>{};
      
      for (final entry in _controllers.entries) {
        formData[entry.key] = entry.value.text;
      }
      
      // Log form submission (without sensitive data)
      FlutterUxcam.logEvent('secure_form_submitted', {
        'form_title': widget.formTitle,
        'field_count': formData.length,
        'has_secure_data': widget.fields.any((f) => f.isSecure && formData[f.key]?.isNotEmpty == true),
        'timestamp': DateTime.now().toIso8601String(),
      });
      
      widget.onSubmit(formData);
    } else {
      // Log validation failure
      FlutterUxcam.logEvent('form_validation_failed', {
        'form_title': widget.formTitle,
        'timestamp': DateTime.now().toIso8601String(),
      });
    }
  }
  
  @override
  void dispose() {
    for (final controller in _controllers.values) {
      controller.dispose();
    }
    super.dispose();
  }
}

class FormFieldConfig {
  final String key;
  final String label;
  final String? hint;
  final TextInputType? keyboardType;
  final bool obscureText;
  final bool isSecure;
  final bool required;
  final String occlusionReason;
  final String? Function(String?)? validator;
  
  FormFieldConfig({
    required this.key,
    required this.label,
    this.hint,
    this.keyboardType,
    this.obscureText = false,
    this.isSecure = false,
    this.required = false,
    this.occlusionReason = 'form_field',
    this.validator,
  });
}
```

### Payment Form Example
```dart
class PaymentForm extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return SecureFormWidget(
      formTitle: 'Payment Information',
      enableOcclusion: true,
      fields: [
        FormFieldConfig(
          key: 'card_number',
          label: 'Card Number',
          hint: '1234 5678 9012 3456',
          keyboardType: TextInputType.number,
          isSecure: true,
          required: true,
          occlusionReason: 'credit_card_number',
          validator: _validateCardNumber,
        ),
        FormFieldConfig(
          key: 'expiry_date',
          label: 'Expiry Date',
          hint: 'MM/YY',
          keyboardType: TextInputType.datetime,
          isSecure: true,
          required: true,
          occlusionReason: 'card_expiry',
          validator: _validateExpiryDate,
        ),
        FormFieldConfig(
          key: 'cvv',
          label: 'CVV',
          hint: '123',
          keyboardType: TextInputType.number,
          obscureText: true,
          isSecure: true,
          required: true,
          occlusionReason: 'card_cvv',
          validator: _validateCVV,
        ),
        FormFieldConfig(
          key: 'cardholder_name',
          label: 'Cardholder Name',
          hint: 'John Doe',
          keyboardType: TextInputType.name,
          isSecure: false, // Name might not need occlusion
          required: true,
          occlusionReason: 'cardholder_name',
        ),
        FormFieldConfig(
          key: 'billing_zip',
          label: 'Billing ZIP Code',
          hint: '12345',
          keyboardType: TextInputType.number,
          isSecure: true,
          required: true,
          occlusionReason: 'billing_address',
        ),
      ],
      onSubmit: (formData) {
        _processPayment(formData);
      },
    );
  }
  
  String? _validateCardNumber(String? value) {
    if (value == null || value.isEmpty) return 'Card number is required';
    final cleaned = value.replaceAll(RegExp(r'\D'), '');
    if (cleaned.length < 13 || cleaned.length > 19) {
      return 'Invalid card number length';
    }
    return null;
  }
  
  String? _validateExpiryDate(String? value) {
    if (value == null || value.isEmpty) return 'Expiry date is required';
    final regex = RegExp(r'^(0[1-9]|1[0-2])\/([0-9]{2})$');
    if (!regex.hasMatch(value)) {
      return 'Invalid expiry date format (MM/YY)';
    }
    return null;
  }
  
  String? _validateCVV(String? value) {
    if (value == null || value.isEmpty) return 'CVV is required';
    if (value.length < 3 || value.length > 4) {
      return 'CVV must be 3 or 4 digits';
    }
    return null;
  }
  
  void _processPayment(Map<String, String> formData) {
    // Process payment securely
    FlutterUxcam.logEvent('payment_form_completed', {
      'has_card_data': formData['card_number']?.isNotEmpty ?? false,
      'timestamp': DateTime.now().toIso8601String(),
    });
  }
}
```

## Dropdown and Selection Occlusion

### Secure Dropdown Field
```dart
class SecureDropdownField<T> extends StatefulWidget {
  final String? labelText;
  final T? value;
  final List<DropdownMenuItem<T>> items;
  final void Function(T?)? onChanged;
  final String? Function(T?)? validator;
  final bool autoOcclude;
  final String occlusionReason;
  
  const SecureDropdownField({
    Key? key,
    this.labelText,
    this.value,
    required this.items,
    this.onChanged,
    this.validator,
    this.autoOcclude = false,
    this.occlusionReason = 'dropdown_field',
  }) : super(key: key);
  
  @override
  _SecureDropdownFieldState<T> createState() => _SecureDropdownFieldState<T>();
}

class _SecureDropdownFieldState<T> extends State<SecureDropdownField<T>> {
  final GlobalKey _dropdownKey = GlobalKey();
  bool _isOpen = false;
  
  @override
  Widget build(BuildContext context) {
    return Container(
      key: _dropdownKey,
      child: DropdownButtonFormField<T>(
        value: widget.value,
        items: widget.items,
        onChanged: (value) {
          widget.onChanged?.call(value);
          
          if (widget.autoOcclude) {
            FlutterUxcam.logEvent('secure_dropdown_changed', {
              'field_label': widget.labelText ?? 'unnamed_dropdown',
              'has_value': value != null,
              'occlusion_reason': widget.occlusionReason,
            });
          }
        },
        validator: widget.validator,
        decoration: InputDecoration(
          labelText: widget.labelText,
          border: OutlineInputBorder(),
          suffixIcon: widget.autoOcclude 
              ? Icon(Icons.security, size: 16, color: Colors.grey)
              : null,
        ),
        onTap: () {
          _isOpen = true;
          if (widget.autoOcclude) {
            _occludeDropdown();
          }
        },
      ),
    );
  }
  
  void _occludeDropdown() {
    WidgetsBinding.instance.addPostFrameCallback((_) {
      final renderBox = _dropdownKey.currentContext?.findRenderObject() as RenderBox?;
      if (renderBox != null) {
        final position = renderBox.localToGlobal(Offset.zero);
        final size = renderBox.size;
        
        // Occlude the dropdown field and potential overlay
        FlutterUxcam.occludeRect(
          position.dx,
          position.dy,
          size.width,
          size.height,
        );
        
        // Also occlude potential dropdown overlay area
        // This is an approximation - actual overlay position varies
        FlutterUxcam.occludeRect(
          position.dx,
          position.dy + size.height,
          size.width,
          200, // Estimated dropdown list height
        );
      }
    });
  }
}
```

## Specialized Input Types

### Secure Pin Input
```dart
class SecurePinInput extends StatefulWidget {
  final int length;
  final void Function(String) onCompleted;
  final void Function(String)? onChanged;
  final bool autoOcclude;
  
  const SecurePinInput({
    Key? key,
    this.length = 4,
    required this.onCompleted,
    this.onChanged,
    this.autoOcclude = true,
  }) : super(key: key);
  
  @override
  _SecurePinInputState createState() => _SecurePinInputState();
}

class _SecurePinInputState extends State<SecurePinInput> {
  late List<TextEditingController> _controllers;
  late List<FocusNode> _focusNodes;
  late List<GlobalKey> _fieldKeys;
  String _currentPin = '';
  
  @override
  void initState() {
    super.initState();
    
    _controllers = List.generate(widget.length, (_) => TextEditingController());
    _focusNodes = List.generate(widget.length, (_) => FocusNode());
    _fieldKeys = List.generate(widget.length, (_) => GlobalKey());
    
    // Add listeners
    for (int i = 0; i < widget.length; i++) {
      _controllers[i].addListener(() => _onPinChanged(i));
      _focusNodes[i].addListener(() => _onFocusChanged(i));
    }
    
    // Apply initial occlusion
    if (widget.autoOcclude) {
      WidgetsBinding.instance.addPostFrameCallback((_) {
        _occludeAllFields();
      });
    }
  }
  
  void _onPinChanged(int index) {
    final value = _controllers[index].text;
    
    if (value.isNotEmpty && index < widget.length - 1) {
      // Move to next field
      _focusNodes[index + 1].requestFocus();
    }
    
    // Update current pin
    _currentPin = _controllers.map((c) => c.text).join();
    widget.onChanged?.call(_currentPin);
    
    // Check if pin is complete
    if (_currentPin.length == widget.length) {
      widget.onCompleted(_currentPin);
      
      if (widget.autoOcclude) {
        FlutterUxcam.logEvent('secure_pin_completed', {
          'pin_length': widget.length,
          'timestamp': DateTime.now().toIso8601String(),
        });
      }
    }
    
    // Re-apply occlusion after changes
    if (widget.autoOcclude) {
      WidgetsBinding.instance.addPostFrameCallback((_) {
        _occludeAllFields();
      });
    }
  }
  
  void _onFocusChanged(int index) {
    if (widget.autoOcclude) {
      WidgetsBinding.instance.addPostFrameCallback((_) {
        _occludeAllFields();
      });
    }
  }
  
  void _occludeAllFields() {
    for (int i = 0; i < widget.length; i++) {
      final renderBox = _fieldKeys[i].currentContext?.findRenderObject() as RenderBox?;
      if (renderBox != null && renderBox.hasSize) {
        final position = renderBox.localToGlobal(Offset.zero);
        final size = renderBox.size;
        
        FlutterUxcam.occludeRect(
          position.dx,
          position.dy,
          size.width,
          size.height,
        );
      }
    }
  }
  
  @override
  Widget build(BuildContext context) {
    return Row(
      mainAxisAlignment: MainAxisAlignment.spaceEvenly,
      children: List.generate(widget.length, (index) {
        return Container(
          key: _fieldKeys[index],
          width: 50,
          height: 60,
          child: TextFormField(
            controller: _controllers[index],
            focusNode: _focusNodes[index],
            textAlign: TextAlign.center,
            keyboardType: TextInputType.number,
            obscureText: true,
            maxLength: 1,
            decoration: InputDecoration(
              counterText: '',
              border: OutlineInputBorder(
                borderRadius: BorderRadius.circular(8),
              ),
            ),
            onChanged: (value) {
              if (value.length > 1) {
                _controllers[index].text = value.substring(value.length - 1);
              }
            },
          ),
        );
      }),
    );
  }
  
  @override
  void dispose() {
    for (final controller in _controllers) {
      controller.dispose();
    }
    for (final focusNode in _focusNodes) {
      focusNode.dispose();
    }
    super.dispose();
  }
}
```

## Form State Management

### Secure Form Provider
```dart
class SecureFormProvider extends ChangeNotifier {
  final Map<String, dynamic> _formData = {};
  final Map<String, bool> _fieldOcclusionStates = {};
  final Map<String, bool> _fieldSecurityLevels = {};
  
  bool _globalOcclusionEnabled = true;
  
  bool get globalOcclusionEnabled => _globalOcclusionEnabled;
  
  void setGlobalOcclusion(bool enabled) {
    _globalOcclusionEnabled = enabled;
    
    // Update all secure fields
    for (final entry in _fieldSecurityLevels.entries) {
      if (entry.value) { // If field is secure
        _fieldOcclusionStates[entry.key] = enabled;
      }
    }
    
    notifyListeners();
    
    FlutterUxcam.logEvent('form_global_occlusion_changed', {
      'enabled': enabled,
      'affected_fields': _fieldSecurityLevels.entries.where((e) => e.value).length,
    });
  }
  
  void registerField(String fieldKey, {required bool isSecure}) {
    _fieldSecurityLevels[fieldKey] = isSecure;
    _fieldOcclusionStates[fieldKey] = isSecure && _globalOcclusionEnabled;
    notifyListeners();
  }
  
  void updateFieldValue(String fieldKey, dynamic value, {bool logInteraction = true}) {
    _formData[fieldKey] = value;
    
    if (logInteraction) {
      FlutterUxcam.logEvent('secure_form_field_updated', {
        'field_key': fieldKey,
        'is_secure': _fieldSecurityLevels[fieldKey] ?? false,
        'has_value': value != null && value.toString().isNotEmpty,
        'timestamp': DateTime.now().toIso8601String(),
      });
    }
    
    notifyListeners();
  }
  
  bool shouldOccludeField(String fieldKey) {
    return _fieldOcclusionStates[fieldKey] ?? false;
  }
  
  void toggleFieldOcclusion(String fieldKey) {
    if (_fieldSecurityLevels[fieldKey] == true) {
      _fieldOcclusionStates[fieldKey] = !(_fieldOcclusionStates[fieldKey] ?? false);
      notifyListeners();
      
      FlutterUxcam.logEvent('field_occlusion_toggled', {
        'field_key': fieldKey,
        'occluded': _fieldOcclusionStates[fieldKey],
      });
    }
  }
  
  Map<String, dynamic> getFormData({bool includeSensitive = true}) {
    if (includeSensitive) {
      return Map.from(_formData);
    } else {
      final filtered = <String, dynamic>{};
      for (final entry in _formData.entries) {
        if (_fieldSecurityLevels[entry.key] != true) {
          filtered[entry.key] = entry.value;
        }
      }
      return filtered;
    }
  }
  
  void clearFormData() {
    _formData.clear();
    notifyListeners();
    
    FlutterUxcam.logEvent('secure_form_data_cleared', {
      'timestamp': DateTime.now().toIso8601String(),
    });
  }
}
```

## Best Practices

### Do's
- ✅ Automatically detect sensitive field types
- ✅ Provide user control over occlusion settings
- ✅ Occlude the entire field, including labels
- ✅ Handle focus states appropriately
- ✅ Log form interactions without sensitive data
- ✅ Test occlusion with different keyboards and input methods

### Don'ts
- ❌ Don't occlude non-sensitive fields unnecessarily
- ❌ Don't log actual sensitive values
- ❌ Don't break form validation with occlusion
- ❌ Don't ignore user accessibility needs
- ❌ Don't apply occlusion synchronously during input
- ❌ Don't forget to handle dynamic form fields

## Testing Form Field Masking

### Form Occlusion Testing
```dart
testWidgets('Secure form fields are properly occluded', (WidgetTester tester) async {
  final occludedFields = <String>[];
  
  // Mock UXCam occlusion
  FlutterUxcam.occludeRect = (x, y, width, height) {
    occludedFields.add('${x}_${y}_${width}_${height}');
  };
  
  await tester.pumpWidget(
    MaterialApp(
      home: Scaffold(
        body: PaymentForm(),
      ),
    ),
  );
  
  await tester.pumpAndSettle();
  
  // Verify that secure fields are occluded
  expect(occludedFields.length, greaterThan(0));
  
  // Test field interaction
  await tester.enterText(find.byType(TextFormField).first, '1234567890123456');
  await tester.pumpAndSettle();
  
  // Verify occlusion is maintained after input
  expect(occludedFields.length, greaterThan(4)); // Should have re-applied occlusion
});
```

## Next Steps

- **[Widget Occlusion Patterns](widget-occlusion-patterns.md)** - General widget privacy protection
- **[User Analytics](../users-and-properties.md)** - Connect forms to user insights
- **[Advanced Configuration](../advanced-configuration-and-apis/)** - Fine-tune privacy settings

---

*Master form field masking to protect sensitive user input while maintaining excellent form usability and validation.*