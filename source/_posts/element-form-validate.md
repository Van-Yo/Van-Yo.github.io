---
title: element条件判断表单验证
author: 11LuckyBOY
index_img: 'https://miro.medium.com/max/1008/1*wLEdf_hAOJ6aqV8_ivW1hg.png'
category: 技术
tags:
  - Element
  - Vue
excerpt: 在中后台系统的日常开发中，表单必不可少，当表单内容比较多加上各种动态联动的表单校验，无疑让我们的页面开发过程雪上加霜，本文将结合自己平时的开发习惯，分享一下在大表单开发中如何处理复杂的表单校验。
date: 2022-01-11 14:52:19
---
## 表单校验
直接上代码：
```js
<template>
  <div class="container">
    <el-form
      ref="ruleForm"
      :rules="rules"
      :model="form"
      inline
      class="form-style"
    >
      <el-form-item label="企业性质" prop="natureEnterprise">
        <el-select v-model="form.natureEnterprise">
          <el-option label="国企" :value="1" />
          <el-option label="事业单位" :value="2" />
          <el-option label="个体户" :value="3" />
        </el-select>
      </el-form-item>
      <el-form-item label="业务类型" prop="type">
        <el-select v-model="form.type">
          <el-option label="护肤" :value="1" />
          <el-option label="食品" :value="2" />
        </el-select>
      </el-form-item>
      <el-form-item label="企业名称" prop="name">
        <el-input v-model="form.name" placeholder="请输入" />
      </el-form-item>
      <el-form-item label="社会统一信用代码" prop="creditCode">
        <el-input v-model="form.creditCode" placeholder="请输入" />
      </el-form-item>
      <el-form-item label="注册地址" prop="address">
        <el-input v-model="form.address" placeholder="请输入" />
      </el-form-item>
      <el-form-item>
        <el-button
          type="primary"
          @click="submitForm('ruleForm')"
        >立即创建</el-button>
        <el-button @click="resetForm('ruleForm')">重置</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      form: {
        natureEnterprise: null,
        type: null,
        address: '',
        name: '',
        creditCode: ''
      },
      rules: {
        natureEnterprise: [
          { required: true, message: '企业性质不能为空', trigger: 'change' }
        ],
        type: [
          { required: true, message: '业务类型不能为空', trigger: 'change' }
        ],
        address: [
          { required: true, message: '注册地址不能为空', trigger: 'change' }
        ],
        name: [
          { required: true, message: '企业名称不能为空', trigger: 'change' }
        ],
        creditCode: [
          {
            required: true,
            message: '社会统一信用代码不能为空',
            trigger: 'change'
          }
        ]
      }
    }
  },
  methods: {
    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          alert('submit!')
        } else {
          console.log('error submit!!')
          return false
        }
      })
    },
    resetForm(formName) {
      this.$refs[formName].resetFields()
    }
  }
}
</script>

<style lang="scss" scoped>
.container {
  padding: 30px;
  .form-style {
    display: flex;
    flex-direction: column;
  }
}
</style>
```
全部校验：
![](validate-1.jpg)

以上表单为例，要求默认全部必填，如果企业性质为 `个体户` ，则 `注册地址` 和 `社会统一信用代码` 为 `非必填` ，让我们增加以下代码来实现这个需求：
```js
watch: {
  'form.natureEnterprise'(val) {
    this.$refs['ruleForm'].clearValidate()
    this.rules.creditCode[0].required = val !== 3
    this.rules.address[0].required = val !== 3
  }
}
```
通过手动修改 `rules` 中的 `required` 值来动态判断某些字段是否必填：
![](validate-2.jpg)
如果此时 `新增一个校验` ，假设要求业务类型为护肤时，企业名称非必填，那我们需要像上面监听业务类型的值然后做相应的判断，随着表单内容的增多，我们的 `watch` 会越来越多，同时 `rules` 也会散落在不同的地方，这必然会为后续的代码维护带来困难，接手的人也必须小心翼翼的在上面做修改。

## 使用computed进行表单校验优化
将 `rules` 作为计算属性里的值统一处理，而不是放在 `data` 里，避免需要频繁去操作 `data` 里的 `rules` ，也避免 `rules` 的项散落在页面各处[^1]。
```js
computed: {
  rules({ form }) {
    // 是否个体户
    const isSelfEmploy = form.natureEnterprise === 3
    return {
      natureEnterprise: [
        { required: true, message: '企业性质不能为空', trigger: 'change' }
      ],
      type: [
        { required: true, message: '业务类型不能为空', trigger: 'change' }
      ],
      name: [
        { required: true, message: '企业名称不能为空', trigger: 'change' }
      ],
      address: [
        {
          required: !isSelfEmploy,
          message: '注册地址不能为空',
          trigger: 'change'
        }
      ],
      creditCode: [
        {
          required: !isSelfEmploy,
          message: '社会统一信用代码不能为空',
          trigger: 'change'
        }
      ]
    }
  }
},
```
改用 `computed` 后，会有一个问题：页面初始加载或 `computed` 里使用的相关值改变时会 `立即触发检验` 。看起来体验不是很好， `el-form` 有一个 `validate-on-rule-change` 属性，表示会在 `rules` 属性改变后立即触发一次验证，默认为 `true` ，将其设置为 `false` 即可。
```html
<el-form
  :validate-on-rule-change="false"
></el-form>
```

## 表单拆分
当表单内容变得庞大时，将其塞在一个文件里进行开发时无疑会变得很臃肿。这时候我们可以将其拆分成一个一个的组件，每个组件里独立负责自己的表单内容以及校验，最后提交时由父组件统一收集每个子组件的数据进行提交，这样避免了所有内容都放在一个文件里变得大而杂，同时也有利于团队多人进行开发，减少冲突。
```js
// 父组件：main-form.vue
<template>
  <!-- 子组件-基础信息表单 -->
  <base-form ref="baseFormRef" />
  <!-- 子组件-合作信息表单 -->
  <coop-form ref="coopFormRef" />
  
  <el-button type="primary" @click="handleSave">保存</el-button>
</template>

<script>
import BaseForm from './base-form'
import CoopForm from './coop-form'

export default {
  components: {
    BaseForm,
    CoopForm
  },
  
  methods: {
    async handleSave() {
      // 调用子组件提供的方法，获取表单数据
      const baseFormData = await this.$refs['baseFormRef'].validate()
      const coopFormData = await this.$refs['coopFormRef'].validate()
      if (baseFormData && coopFormData) {
        // 如果校验通过，拼接子组件数据进行提交
        const mainFormData = {
          ...baseFormData,
          ...coopFormData
        }
        // 提交数据
        await saveApi(mainForm)
        this.$message.success('保存成功！')
      }
    }
  }
}
</script>
```
子组件负责处理自己的内容，同时提供方法给父组件调用，在方法里进行校验，校验通过后再返回自身的表单数据给父组件。
```js
// 子组件示例：base-form.vue
<script>
export default {
  data() {
    return {
      form: {
        // ...
      }
    }
  },
  computed: {
    rules({ form }) {
      return {
        // ...
      }
    }
  },
  methods: {
    // 提供给父组件的方法并返回表单数据
    validate() {
      return new Promise(resolve => {
        this.$refs['ruleForm'].validate(valid => {
          // 校验通过返回表单数据，反之，返回null
          if (valid) {
            resolve(this.form)
          } else {
            resolve(null)
          }
        })
      })
    }
  }
}
</script>
```
## 完整参考代码
```js
<template>
  <div class="container">
    <el-form
      ref="ruleForm"
      :rules="rules"
      :model="form"
      :validate-on-rule-change="false"
      inline
      class="form-style"
    >
      <el-form-item label="企业性质" prop="natureEnterprise">
        <el-select v-model="form.natureEnterprise">
          <el-option label="国企" :value="1" />
          <el-option label="事业单位" :value="2" />
          <el-option label="个体户" :value="3" />
        </el-select>
      </el-form-item>
      <el-form-item label="业务类型" prop="type">
        <el-select v-model="form.type">
          <el-option label="护肤" :value="1" />
          <el-option label="食品" :value="2" />
        </el-select>
      </el-form-item>
      <el-form-item label="企业名称" prop="name">
        <el-input v-model="form.name" placeholder="请输入" />
      </el-form-item>
      <el-form-item label="社会统一信用代码" prop="creditCode">
        <el-input v-model="form.creditCode" placeholder="请输入" />
      </el-form-item>
      <el-form-item label="注册地址" prop="address">
        <el-input v-model="form.address" placeholder="请输入" />
      </el-form-item>
      <el-form-item>
        <el-button
          type="primary"
          @click="submitForm('ruleForm')"
        >立即创建</el-button>
        <el-button @click="resetForm('ruleForm')">重置</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      form: {
        natureEnterprise: null,
        type: null,
        address: '',
        name: '',
        creditCode: ''
      }
    }
  },
  computed: {
    rules({ form }) {
      // 是否个体户
      const isSelfEmploy = form.natureEnterprise === 3
      return {
        natureEnterprise: [
          { required: true, message: '企业性质不能为空', trigger: 'change' }
        ],
        type: [
          { required: true, message: '业务类型不能为空', trigger: 'change' }
        ],
        name: [
          { required: true, message: '企业名称不能为空', trigger: 'change' }
        ],
        address: [
          {
            required: !isSelfEmploy,
            message: '注册地址不能为空',
            trigger: 'change'
          }
        ],
        creditCode: [
          {
            required: !isSelfEmploy,
            message: '社会统一信用代码不能为空',
            trigger: 'change'
          }
        ]
      }
    }
  },
  methods: {
    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          alert('submit!')
        } else {
          console.log('error submit!!')
          return false
        }
      })
    },
    resetForm(formName) {
      this.$refs[formName].resetFields()
    }
  }
}
</script>
<style lang="scss" scoped>
.container {
  padding: 30px;
  .form-style {
    display: flex;
    flex-direction: column;
  }
}
</style>
```

## 参考
[^1]: [中后台系统中，复杂表单的开发优化技巧](https://juejin.cn/post/7033910308057907213)