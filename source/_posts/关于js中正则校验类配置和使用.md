---
uuid: ebd86844-c25b-5800-0a35-7cdc4db77af6
title: 关于js中正则校验类配置和使用
categories: 技术
tags: 
- 正则
---
## 校验类
### regexp.js
```js
/**
 * 使用方法 导入 import {regexp} from '@cu/regexp'; //只导入验证类，自己做判断
 *              import Validator from '@cu/regexp'; //导入配置类，使用配置方法进行验证
 * regexp直接做正则验证，返回验证结果
 * console.log(regexp.isIdcard(value)); //检验是否为合法身份证号
 * Validator 验证一系列值，逐个验证
 * 先配置 Validator 规则
 * Validator.add('123',[{
 *      'strategy' : 'maxLength:3',
 *      'errorMsg' : '错误信息'
 * },{
 *      'strategy' : 'checkMobile',
 *      'errorMsg' : '非法手机号'
 * }]);
 * 其中第二个参数为数组，可传入多个验证对象 strategy 对应的验证规则为Validator类的方法 errorMsg对应验证失败后返回的消息
 * 配置完成后启动检验，如果不通过，会把不通过的错误消息返回，验证通过则不返回消息，
 * 配置规则使用的闭包变量，在配置后，如果因为其他原因没进行本次校验，可调用clearRules 清除已配置的规则 避免重复配置规则
 * console.log(Validator.start());
 */
const REGEX_MOBILE = /(1[0-9]{10}$)/; //手机号验证校验规则
const REGEX_CALL = /^0{0,1}(13[0-9]|15[7-9]|153|156|18[7-9])[0-9]{8}|(?:0(?:10|2[0-57-9]|[3-9]\d{2})-)?\d{7,8}$/; //手机号座机号验证校验规则
const REGEX_IDCARD = /^[1-9][0-9]{5}(19|20)[0-9]{2}((01|03|05|07|08|10|12)(0[1-9]|[1-2][0-9]|3[0|1])|(04|06|09|11)(0[1-9]|[1-2][0-9]|30)|02(0[1-9]|[1-2][0-9]))[0-9]{3}([0-9]|x|X)$/; //身份证号校验规则
const REGEX_MEDICALCARD = /^[0-9]{10}$/; //医保卡校验规则
const REGEX_SOCIALCARD = /^[A-Z][A-Z0-9]{8}$/; //社保卡校验规则
const REGEX_SELFPAYCARD = /[A-Z0-9]{15}$/; //自费卡校验规则
const REGEX_EMJOR = /[\uD83C|\uD83D|\uD83E][\uDC00-\uDFFF][\u200D|\uFE0F]|[\uD83C|\uD83D|\uD83E][\uDC00-\uDFFF]|[0-9|*|#]\uFE0F\u20E3|[0-9|#]\u20E3|[\u203C-\u3299]\uFE0F\u200D|[\u203C-\u3299]\uFE0F|[\u2122-\u2B55]|\u303D|[\A9|\AE]\u3030|\uA9|\uAE|\u3030/ig;

/**
 * 校验正则类
 */
class Regexp {
    constructor() {

    }

    /**
     * 检测是不是手机号
     * @param {检测字符串} str
     */
    isMobile(str) {
        return REGEX_MOBILE.test(str);
    }

    /**
     * 检测是不是身份证号
     * @param {检测字符串} str
     */
    isIdcard(str) {
        return REGEX_IDCARD.test(str);
    }

    /**
     * 检测是不是医保卡
     * @param {检测字符串} str
     */
    isMedicalcard(str) {
        return REGEX_MEDICALCARD.test(str);
    }

    /**
     * 检测是不是社保卡
     * @param {检测字符串} str
     */
    isSocialcard(str) {
        return REGEX_SOCIALCARD.test(str);
    }

    /**
     * 检测是不是自费卡
     * @param {检测字符串} str
     */
    isSelfpaycard(str) {
        return REGEX_SELFPAYCARD.test(str);
    }

    /**
     * 检查是否是emjor表情
     * @param {*} str
     */
    isEmjor(str) {
        return REGEX_EMJOR.test(str);
    }

    /**
     * 检查是否是手机号座机号
     * @param {*} str
     */
    isCall(str) {
        return REGEX_CALL.test(str);
    }
}

const regexp = new Regexp();

/**
 * Strategies 策略类，配置校验规则，返回错误提示语信息
 */
class Strategies {
    constructor() {

    }

    /**
     * 检测是否空值
     * @param {检测值} value
     * @param {错误信息} errorMsg
     */
    checkNotEmpty(value, errorMsg) {
        if (value) {
            if (!value.length) {
                return errorMsg;
            }
        } else {
            return errorMsg;
        }
    }

    /**
     *
     * @param {检测值} value
     * @param {限制最小长度} length
     * @param {错误信息} errorMsg
     */
    checkMinLen(value, length, errorMsg) {
        if (value && value.length < length) {
            return errorMsg;
        }
    }

    /**
     *
     * @param {检测值} value
     * @param {限制最大长度} length
     * @param {错误信息} errorMsg
     */
    checkMaxLen(value, length, errorMsg) {
        if (value && value.length > length) {
            return errorMsg;
        }
    }

    /**
     * 检查手机号
     * @param {检测值} value
     * @param {错误信息} errorMsg
     */
    checkMobile(value, errorMsg) {
        if (!regexp.isMobile(value)) {
            return errorMsg;
        }
    }

    /**
     * 检查手机号座机号
     * @param {检测值} value
     * @param {错误信息} errorMsg
     */
    checkCall(value, errorMsg) {
        if (!regexp.isCall(value)) {
            return errorMsg;
        }
    }

    // 护照
    isPassPortCard(card) {
        // 护照
        // 规则： 14/15开头 + 7位数字, G + 8位数字, P + 7位数字, S/D + 7或8位数字,等
        // 样本： 141234567, G12345678, P1234567
        var reg = /^([a-zA-z]|[0-9]){5,17}$/;
        if (reg.test(card) === false) {
            return '护照号码不合规';
        }
    }

    // 军人证
    isOfficerCard(card) {
        // 军官证
        // 规则： 军/兵/士/文/职/广/（其他中文） + "字第" + 4到8位字母或数字 + "号"
        // 样本： 军字第2001988号, 士字第P011816X号
        var reg = /^[\u4E00-\u9FA5](字第)([0-9a-zA-Z]{4,8})(号?)$/;
        if (reg.test(card) === false) {
            return '军官证号不合规';
        }
    }

    // 户口本
    isAccountCard(card) {
        // 户口本
        // 规则： 15位数字, 18位数字, 17位数字 + X
        // 样本： 441421999707223115
        var reg = /(^\d{15}$)|(^\d{18}$)|(^\d{17}(\d|X|x)$)/;
        if (reg.test(card) === false) {
            return '户口本号码不合规';
        }
    }

    /**
     * 检测身份证号
     * @param {检测值} value
     */
    checkIdcard(val) {
        // 省级地址码校验
        var checkProv = function (val) {
            var pattern = /^[1-9][0-9]/;
            var provs = {
                11: '北京',
                12: '天津',
                13: '河北',
                14: '山西',
                15: '内蒙古',
                21: '辽宁',
                22: '吉林',
                23: '黑龙江 ',
                31: '上海',
                32: '江苏',
                33: '浙江',
                34: '安徽',
                35: '福建',
                36: '江西',
                37: '山东',
                41: '河南',
                42: '湖北 ',
                43: '湖南',
                44: '广东',
                45: '广西',
                46: '海南',
                50: '重庆',
                51: '四川',
                52: '贵州',
                53: '云南',
                54: '西藏 ',
                61: '陕西',
                62: '甘肃',
                63: '青海',
                64: '宁夏',
                65: '新疆',
                71: '台湾',
                81: '香港',
                82: '澳门'
            };
            if (pattern.test(val)) {
                if (provs[val]) {
                    return true;
                }
            }
            return false;
        };
        // 出生日期码校验
        var checkDate = function (val) {
            // eslint-disable-next-line no-useless-escape
            var pattern = /^(18|19|20)\d{2}((0[1-9])|(1[0-2]))(([0-2][1-9])|10|20|30|31)$/;
            if (pattern.test(val)) {
                var year = val.substring(0, 4);
                var month = val.substring(4, 6);
                var date = val.substring(6, 8);
                var date2 = new Date(year + '-' + month + '-' + date);
                if (date2 && date2.getMonth() == (parseInt(month) - 1)) {
                    return true;
                }
            }
            return false;
        };
        // 校验码校验
        var checkCode = function (val) {
            // eslint-disable-next-line no-useless-escape
            var p = /^[1-9]\d{5}(18|19|20)\d{2}((0[1-9])|(1[0-2]))(([0-2][1-9])|10|20|30|31)\d{3}[0-9Xx]$/;
            var factor = [7, 9, 10, 5, 8, 4, 2, 1, 6, 3, 7, 9, 10, 5, 8, 4, 2];
            var parity = [1, 0, 'X', 9, 8, 7, 6, 5, 4, 3, 2];
            var code = val.substring(17);
            if (p.test(val)) {
                var sum = 0;
                for (var i = 0; i < 17; i++) {
                    sum += val[i] * factor[i];
                }
                if (parity[sum % 11] == code.toUpperCase()) {
                    return true;
                }
            }
            return false;
        };
        if (checkCode(val)) {
            var date = val.substring(6, 14);
            if (checkDate(date)) {
                if (checkProv(val.substring(0, 2))) {
                    //pass
                } else {
                    return '身份证号地区码不符';
                }
            } else {
                return '身份证号日期码不符';
            }
        } else {
            return '身份证号校验码不符';
        }
    }

    /**
     * 检测医保卡
     * @param {检测值} value
     * @param {错误信息} errorMsg
     */
    checkMedicalcard(value, errorMsg) {
        if (!regexp.isMedicalcard(value)) {
            return errorMsg;
        }
    }

    /**
     * 检测社保卡
     * @param {检测值} value
     * @param {错误信息} errorMsg
     */
    checkSocialcard(value, errorMsg) {
        if (!regexp.isSocialcard(value)) {
            return errorMsg;
        }
    }

    /**
     * 检查自费卡
     * @param {检测值} value
     * @param {错误信息} errorMsg
     */
    checkSelfPaycard(value, errorMsg) {
        if (!regexp.isSelfpaycard(value)) {
            return errorMsg;
        }
    }
}

const strategies = new Strategies();

/**
 * 根据校验规则进行校验
 */
class Validator {
    constructor() {
        this.validatorRules = []; //校验规则
    }

    /**
     * 添加检验规则
     * @param {配置校验} value
     * @param {*} rules
     */
    add(value, rules) {
        for (let i = 0, rule;
             (rule = rules[i++]);) {
            let strategyAry = rule.strategy.split(':');
            let errorMsg = rule.errorMsg;
            this.validatorRules.push(() => {
                let strategy = strategyAry.shift();
                strategyAry.unshift(value);
                strategyAry.push(errorMsg);
                return strategies[strategy] && strategies[strategy].apply(value, strategyAry);
            });
        }
    }

    /**
     * 启动校验
     */
    start() {
        for (let i = 0, validatorFunc;
             (validatorFunc = this.validatorRules[i++]);) {
            let errorMsg = validatorFunc();
            if (errorMsg) {
                //本次检验失败，清空校验规则
                this.clearRules();
                return errorMsg;
            }
        }
    }

    /**
     * 清空校验规则
     */
    clearRules() {
        this.validatorRules = [];
    }
}

const validator = new Validator();
export {
    regexp,
    validator as default
};
```
## 使用范例
### index.js
```js
import validator from '@cu/regexp.js';
submitSignForm() {
    if (!this.submitInfo.jjlxfs) {
        this.$toast('请输入紧急联系方式 Please enter the contact phone number');
        return;
    } else {
        validator.add(this.submitInfo.jjlxfs, [{
            strategy: 'checkCall',
            errorMsg: '请输入正确格式的手机号/座机号 Please enter the correct number'
        }]);
        let msg = validator.start();
        if (msg) {
            this.$toast(msg);
            return;
        }
    }
}
```