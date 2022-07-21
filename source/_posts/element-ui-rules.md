---
date: 2020-10-15
title: el-form 表单校验规则封装
categories: [Vue, Element]
tags: [封装, 表单校验, Vue, Element]
cover: https://tva4.sinaimg.cn/mw690/006wuklaly1gjqe84ot7oj31c00u0wij.jpg
references:
  - title: 'ElementUI表单校验rules封装'
    url: https://blog.csdn.net/xhom_w/article/details/103961710
---


## 前言
`el-form` 表单验证如果字段过多则需要写很多重复的语句显得非常冗余，如采用封装则只需对外暴露 `vxRule()` 方法即可，大大提高了开发效率，该工具类验证规则是可以自行拓展的，满足大部分开发需求

### 封装前：
{% noteblock child:codeblock color:red %}
```js
form_rules: {
  categoryId: [
    { required: true, message: '传感器类型不能为空', trigger: 'blur' }
  ],
  sn: [{ required: true, message: '序号不能为空', trigger: 'blur' }],
  dixtance: [
    { required: true, message: '测量点距离不能为空', trigger: 'blur' }
  ],
  name: [
    { required: true, message: '传感器名称不能为空', trigger: 'blur' }
  ],
  measure: [
    { required: true, message: '测量量不能为空', trigger: 'blur' }
  ],
  unit: [{ required: true, message: '单位不能为空', trigger: 'blur' }],
  start: [
    { required: true, message: '测量下限不能为空', trigger: 'blur' },
    { validator: ruleCheck.isNumberCheck, trigger: 'blur' }
  ],
  end: [
    { required: true, message: '测量上限不能为空', trigger: 'blur' },
    { validator: ruleCheck.isNumberCheck, trigger: 'blur' }
  ],
  depthA: [
    { required: true, message: 'A基点深度不能为空', trigger: 'blur' },
    { validator: ruleCheck.isNumberCheck, trigger: 'blur' }
  ],
  // 等.....
```
{% endnoteblock %}

### 封装后：
{% noteblock child:codeblock color:green %}
```js
rules: {
  /**
   * @method vxRule()
   * @param required 是否必填
   * @param type 校验类型
   * @param trigger 触发条件
   * @param nullMsg 提示语
   * @desc 📝详细内容见 elFromValidator.js
   * @date 2020-10-09 16:06:53
  */
  sn: vxRule(),
  categoryId: vxRule(true, null, 'change', '请选择传感器类型'),
  dixtance: vxRule(),
  name: vxRule(),
  measure: vxRule(),
  unit: vxRule(),
  start: vxRule(true, "Number"),
  end: vxRule(true, "Number"),
  depthA: vxRule(true, "Number"),
```
{% endnoteblock %}



## elFromValidator.js

直接上代码，注释中都明确注释了，这里不再赘述，验证规则可自行拓展
```js
// 校验规则列表（可扩展）
const rules = {
  URL(url) {
    const regex = /^(https?|ftp):\/\/([a-zA-Z0-9.-]+(:[a-zA-Z0-9.&%$-]+)*@)*((25[0-5]|2[0-4][0-9]|1[0-9]{2}|[1-9][0-9]?)(\.(25[0-5]|2[0-4][0-9]|1[0-9]{2}|[1-9]?[0-9])){3}|([a-zA-Z0-9-]+\.)*[a-zA-Z0-9-]+\.(com|edu|gov|int|mil|net|org|biz|arpa|info|name|pro|aero|coop|museum|[a-zA-Z]{2}))(:[0-9]+)*(\/($|[a-zA-Z0-9.,?"\\+&%$#=~_-]+))*$/
    return valid(url, regex, "URL格式不正确")
  },

  LowerCase(str) {
    const regex = /^[a-z]+$/
    return valid(str, regex, "只能输入小写字母")
  },

  UpperCase(str) {
    const regex = /^[A-Z]+$/
    return valid(str, regex, "只能输入大写字母")
  },

  Alphabets(str) {
    const regex = /^[A-Za-z]+$/
    return valid(str, regex, "只能输入字母")
  },

  Email(email) {
    const regex = /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/
    return valid(email, regex, "邮箱地址格式不正确")
  },

  Mobile(mobile) {
    const regex = /^1\d{10}$/
    return valid(mobile, regex, "手机号格式不正确")
  },

  Phone(phone) {
    const regex = /^(0\d{2,3})?-?\d{7,8}$/
    return valid(phone, regex, "电话号码格式不正确")
  },

  Postcode(postcode) {
    const regex = /^[0-9][0-9]{5}$/
    return valid(postcode, regex, "邮编格式不正确")
  },

  IpAddress(ip) {
    const regex = /^(\d{1,2}|1\d\d|2[0-4]\d|25[0-5])\.(\d{1,2}|1\d\d|2[0-4]\d|25[0-5])\.(\d{1,2}|1\d\d|2[0-4]\d|25[0-5])\.(\d{1,2}|1\d\d|2[0-4]\d|25[0-5])$/;
    return valid(ip, regex, "IP地址输入格式不合法")
  },

  MacAddress(mac) {
    const regex = /^[A-F0-9]{2}(-[A-F0-9]{2}){5}$/;
    return valid(mac, regex, "Mac地址输入格式不合法")
  },

  Number(num) {
    const regex = /^\d+$/
    return valid(num, regex, "只能输入纯数字")
  },

  Fax(fax) {
    const regex = /^(\d{3,4}-)?\d{7,8}$/
    return valid(fax, regex, "传真格式不正确")
  },

  Int(num) {
    const regex = /^((0)|([1-9]\d*))$/
    return valid(num, regex, "只能输入非负整数")
  },

  IntPlus(num){
    const regex = /^[1-9]\d*$/
    return valid(num, regex, "只能输入正整数")
  },

  Float1(num){
    const regex = /^-?\d+(\.\d)?$/
    return valid(num, regex, "只能输入数字，最多一位小数")
  },

  Float2(num){
    const regex = /^-?\d+(\.\d{1,2})?$/
    return valid(num, regex, "只能输入数字，最多两位小数")
  },

  Float3(num){
    const regex = /^-?\d+(\.\d{1,3})?$/
    return valid(num, regex, "只能输入数字，最多三位小数")
  },

  FloatPlus3(num){
    const regex = /^\d+(\.\d{1,3})?$/
    return valid(num, regex, "只能输入数字，最多三位小数")
  },

  FloatPlus4(num){
    const regex = /^\d+(\.\d{1,4})?$/
    return valid(num, regex, "只能输入数字，最多四位小数")
  },

  Encode(code){
    const regex = /^(_|-|[a-zA-Z0-9])+$/
    return valid(code, regex, "编码只能使用字母、数字、下划线、中划线")
  },

  Encode2(code){
    const regex = /^[a-zA-Z0-9]+$/
    return valid(code, regex, "编码只能使用字母、数字")
  },

  Encode3(code){
    const regex = /^(_|[a-zA-Z0-9])+$/
    return valid(code, regex, "编码只能使用字母、数字、下划线")
  },

  IdCard(code){
    const regex = /^[1-9]\d{5}(18|19|([23]\d))\d{2}((0[1-9])|(10|11|12))(([0-2][1-9])|10|20|30|31)\d{3}[0-9Xx]$/
    return valid(code, regex, "请输入正确的身份证号码")
  },

  USCC(code){
    const regex = /^[0-9A-Z]{18}/
    return valid(code, regex, "请输入正确的社会信用号")
  },

  CarNum(code){
    const regex = /^(([京津沪渝冀豫云辽黑湘皖鲁新苏浙赣鄂桂甘晋蒙陕吉闽贵粤青藏川宁琼使领][A-Z](([0-9]{5}[DF])|([DF]([A-HJ-NP-Z0-9])[0-9]{4})))|([京津沪渝冀豫云辽黑湘皖鲁新苏浙赣鄂桂甘晋蒙陕吉闽贵粤青藏川宁琼使领][A-Z][A-HJ-NP-Z0-9]{4}[A-HJ-NP-Z0-9挂学警港澳使领]))$/i
    return valid(code, regex, "请输入正确的车牌号")
  },

  CNandEN(code){
    const regex = /^[a-zA-Z\u4e00-\u9fa5]+$/
    return valid(code, regex, "只能使用中文、英文")
  },

  MobileOrPhone(val){
  	const result = /^1\d{10}$/.test(val) || /^(0\d{2,3})?-?\d{7,8}$/.test(val)
  	return valid(result, null, "手机或电话号格式不正确")
  }
}


/**
 * @method valid()
 * @param {String} val  要校验的值
 * @param {regex:RegExp} regex 校验正则,不是正则时val作为result的值
 * @param {String} msg 校验不通过的错误信息
 * @desc 📝instanceof 运算符用来测试一个对象在其原型链中是否存在一个构造函数的 prototype 属性。
 * @desc 📝语法：object instanceof constructor
 * @desc 📝参数：object（要检测的对象.）constructor（某个构造函数）
 * @desc 📝描述：instanceof 运算符用来检测 constructor.prototype 是否存在于参数 object 的原型链上。
 * @date 2020-10-10 08:56:02
 */
function valid(val, regex, msg){
  return { result: regex instanceof RegExp ? regex.test(val) : !!val, errMsg: msg }
}

/**
* @method vxRule()
* @param {boolean} required 是否必填，选填，默认"true"
* @param {String/Function}type 校验类型，选填
* @desc 📝(String时必须是上面rules中存在的函数名)
* @desc 📝(Function时只接收一个参数(输入值)，返回格式： {result:Boolean, errMsg:String})
* @param {String}trigger 触发动作，选填，默认"blur"
* @param {String}nullMsg 未输入的提示语，选填，required=true时有效
* @date 2020-10-10 09:06:33
*/
export function vxRule(required=true, type, trigger="blur", nullMsg="该字段为必填项") {
  const rule = { required: !!required, trigger}
  let check = null
  if(typeof type === "function"){
    check = type
  }else{
    check = type ? rules[type+""] : null
  }

  if(check){// 存在规则时添加规则
    rule.validator = (r, v, c) => {
      const {result, errMsg} = check(v)
      if(required){
        // 必填项: null,undefined,"","  " 都算无输入内容
        return (v==null || (v+"").trim()==="") ? c(new Error(nullMsg)) : result ? c() : c(new Error(errMsg))
      }
      // 选填项: null,undefined,"" 都算无输入内容，"  "会被校验
      return (v==null || (v+"")==="" || result) ? c() : c(new Error(errMsg))
    }
  }else{
    rule.message = nullMsg
  }

  return [rule]
}
```
## 使用 elFromValidator.js
```js
import { vxRule } from '@/utils/elFromValidator';

export default {
  data() {
    return {
      /**
       * @method vxRule()
       * @param required 是否必填
       * @param type 校验类型
       * @param trigger 触发条件
       * @param nullMsg 提示语
       * @desc 📝详细内容见 elFromValidator.js
       * @date 2020-10-09 16:06:53
      */
      rules: {
        // 该字段为必填，参数为空时使用默认提示语
        sn: vxRule(),
        // 必填，change触发，为空时使用自定义提示语
        categoryId: vxRule(true, null, 'change', '请选择传感器类型'),
        dixtance: vxRule(),
        name: vxRule(),
        //必填，使用rules中的校验规则，trigger为空时默认blur触发，msg为空时使用默认提示语
        start: vxRule(true, "Number"),
        depthA: vxRule(true, "Number"),
        alarmMax: vxRule(true, "FloatPlus4"),
        passId: vxRule(true, null, 'change', '请选择通道'),
        manufacturer: vxRule(),
        sensorType: vxRule(),
        ks1: vxRule(true, "FloatPlus4"),
        precision: vxRule(true, "IntPlus"),
        wave1: vxRule(true, "Number"),
        //选填，blur触发，使用rules中的校验规则
        fff: vxRule(false, "Float3"),
        //选填，blur触发，使用自定义校验规则
        ggg: vxRule(false, val=>{
          return {result: /^[a-z]+$/.test(val), errMsg: "只能输入小写字母"}
        })
      },
    }
  },
};
```

## 结语
> 今后，我要单纯正直地行事。不懂的，就说不懂；不会的，就坦承不会。若是摒弃故作姿态，人生之路似乎是意外的平坦通途
