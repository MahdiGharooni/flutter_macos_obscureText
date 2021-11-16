# Flutter macos obscureText

ObsecureText parameters for **TextFormField** in some macos versions couse hanging and freezing the Flutter application.
Instead of use simple TextFormField with **obsecure = true**, you can build a new widget called **PasswordTextFormFiled** to handle secure characters beside obsecureText = false.


> import 'package:flutter/material.dart';  
import 'package:sun/helpers/platforms.dart' as ph;  
import 'package:sun/sun_icons.dart';  
import 'package:sun/widgets/text_form_fields/base_text_form_field.dart';    
 class PasswordTextFormField extends BaseTextFormField {  
  PasswordTextFormField({  
    @required TextEditingController controller,  
  bool enabled = true,  
  bool ignoreValidation,  
  String labelText,  
  VoidCallback showSelectionDialog,  
  FocusNode focusNode,  
  String hintText,  
  VoidCallback onEditingComplete,  
  ValueChanged<String> validator,  
  TextInputType keyboardType = TextInputType.phone,  
  int maxLength,  
  AutovalidateMode autoValidateMode = AutovalidateMode.onUserInteraction,  
  TextDirection textDirection = TextDirection.ltr,  
  TextDirection hintTextDirection = TextDirection.ltr,  
  String counterText = '',  
  bool obscureText = true,  
 this.hasSuffixIcon = true,  
  ValueChanged<String> onChanged,  
  }) : super(  
            controller: controller,  
  enabled: enabled,  
  ignoreValidation: ignoreValidation,  
  labelText: labelText,  
  showSelectionDialog: showSelectionDialog,  
  focusNode: focusNode,  
  hintText: hintText,  
  onEditingComplete: onEditingComplete,  
  validator: validator,  
  keyboardType: keyboardType,  
  maxLength: maxLength,  
  autoValidateMode: autoValidateMode,  
  textDirection: textDirection,  
  hintTextDirection: hintTextDirection,  
  counterText: counterText,  
  obscureText: obscureText,  
  onChanged: onChanged);  
 final bool hasSuffixIcon;  
  @override  
  State<StatefulWidget> createState() => _PasswordTextFormFieldState();  
}  
class _PasswordTextFormFieldState extends State<PasswordTextFormField> {  
  String _value = '';  
 final TextEditingController _textEditingController = TextEditingController();  
 final platform = ph.getPlatform();  
 var browser;  
  @override  
  void initState() {  
    super.initState();  
  widget.controller.addListener(() {  
      if (widget.controller.text.isNotEmpty) {  
        _textEditingController.text = '';  
 for (int _i = 0; _i < widget.controller.text.length; _i++) {  
          _textEditingController.text += '•';  
  }  
        _value = widget.controller.text;  
  }  
    });  
 if (platform == ph.PlatformType.web) {  
      browser = ph.Browser();  
  }  
  }  
  @override  
  Widget build(BuildContext context) {  
    /// Safari  
  if (platform == ph.PlatformType.web && browser.browser == 'Safari') {  
      return TextFormField(  
        autovalidateMode: widget.autoValidateMode,  
  onEditingComplete: widget.onEditingComplete,  
  focusNode: widget.focusNode,  
  textAlign: TextAlign.end,  
  enabled: widget.enabled,  
  textDirection: widget.textDirection,  
  keyboardType: TextInputType.phone,  
  controller: _textEditingController,  
  validator: (value) => widget.validator?.call(value),  
  maxLength: widget.maxLength,  
  textInputAction: TextInputAction.next,  
  obscureText: false,  
  maxLines: 1,  
  style: TextStyle(fontSize: 14),  
  onChanged: (value) async {  
          if (value.isNotEmpty) {  
            // when value has only dot, it means user remove characters  
  if (RegExp(r'^(•){0,}$').hasMatch(value)) {  
              int _baseOffset = _textEditingController.selection.baseOffset;  
  // offset of changed character  
  int _deletedCount = _value.length - value.length;  
  String _part1 = _value.substring(0, _baseOffset);  
  String _part2 = _value.substring(_baseOffset + _deletedCount);  
  _value = _part1 + _part2;  
  _textEditingController.text = '';  
 for (int _i = 0; _i < _value.length; _i++) {  
                _textEditingController.text += '•';  
  }  
            } else {  
              // add a character and replace it with a dot  
  _textEditingController.text = '';  
  _value = _value + value.replaceAll('•', '');  
 for (int _i = 0; _i < _value.length; _i++) {  
                _textEditingController.text += '•';  
  }  
            }  
          } else {  
            // when user delete all characters  
  _value = '';  
  _textEditingController.text = '';  
  }  
          widget.controller.text = _value;  
  // move cursor to end of text  
  _textEditingController.selection = TextSelection.fromPosition(  
            TextPosition(offset: _textEditingController.text.length),  
  );  
  },  
  decoration: InputDecoration(  
          labelText: widget.labelText,  
  hintText: widget.hintText,  
  hintTextDirection: widget.hintTextDirection,  
  counterText: widget.counterText,  
  contentPadding: EdgeInsets.symmetric(vertical: 12, horizontal: 24),  
  suffixIcon: widget.hasSuffixIcon  
  ? Padding(  
                  padding: const EdgeInsets.only(left: 8),  
  child: Icon(SunIcons.lock),  
  )  
              : Container(width: 0, height: 0,),  
  ),  
  );  
  } else {  
      /// android, chrome & others  
  return TextFormField(  
        autovalidateMode: widget.autoValidateMode,  
  onEditingComplete: () {  
          widget.onEditingComplete?.call();  
  },  
  focusNode: widget.focusNode,  
  enabled: widget.enabled,  
  textDirection: widget.textDirection,  
  keyboardType: TextInputType.number,  
  textAlign: TextAlign.end,  
  controller: widget.controller,  
  validator: (value) => widget.validator?.call(value),  
  maxLength: widget.maxLength,  
  textInputAction: TextInputAction.next,  
  obscureText: widget.obscureText,  
  style: TextStyle(fontSize: 14),  
  maxLines: 1,  
  onChanged: (value) async {  
          widget.onChanged?.call(value);  
  },  
  decoration: InputDecoration(  
          labelText: widget.labelText,  
  hintText: widget.hintText,  
  hintTextDirection: widget.hintTextDirection,  
  contentPadding: EdgeInsets.symmetric(  
            horizontal: 24,  
  vertical: 16,  
  ),  
  counterText: widget.counterText,  
  suffixIcon: widget.hasSuffixIcon  
  ? Padding(  
                  padding: const EdgeInsets.only(left: 8),  
  child: Icon(  
                    SunIcons.lock,  
  size: 24,  
  ),  
  )  
              : Container(  
                  width: 0,  
  height: 0,  
  ),  
  ),  
  );  
  }  
  }  
}

 
 
 
 ## sample:
 >PasswordTextFormField(
                        validator: _otpValidator,
                        enabled: enabled,
                        maxLength: 6,
                        keyboardType: TextInputType.phone,
                        textDirection: TextDirection.ltr,
                        controller: _codeController,
                        labelText: VERIFY_CODE,
                        hintText: VERIFY_HINT,
                      )
