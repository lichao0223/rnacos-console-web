<template>
  <div class="container">
    <n-form class="login_form" ref="formRef" :model="model" :rules="rules">
      <n-form-item path="username" label="用户名">
        <n-input
          placeholder="用户名"
          v-model:value="model.username"
          @keydown.enter.prevent
        />
      </n-form-item>
      <n-form-item path="password" label="密码">
        <n-input
          placeholder="密码"
          type="password"
          v-model:value="model.password"
          @keydown.enter.prevent
        />
      </n-form-item>
      <div style="display: flex">
        <n-button type="primary" block="true" @click="submit"> 登陆 </n-button>
      </div>
    </n-form>
  </div>
</template>

<script>
import { defineComponent, ref, reactive } from 'vue';
import { useMessage } from 'naive-ui';
import { userApi } from '@/api/user';
import { useRoute } from 'vue-router';

export default defineComponent({
  setup() {
    window.$message = useMessage();
    let route = useRoute();
    let query = route.query;
    let redirect_url = query.redirect_url || '/';
    var modelRef = reactive({
      username: null,
      password: null
    });
    const rules = {
      username: [
        {
          required: true,
          validator(rule, value) {
            if (!value) {
              return new Error('需要输入用户名');
            }
            return true;
          },
          trigger: ['input', 'blur']
        }
      ],

      password: [
        {
          required: true,
          validator(rule, value) {
            if (!value) {
              return new Error('需要输入密码');
            }
            return true;
          },
          trigger: ['input', 'blur']
        }
      ]
    };
    var submit = function () {
      const param = {
        username: modelRef.username,
        password: modelRef.password
      };
      userApi
        .login(param)
        .then((res) => {
          if (res.status == 200) {
            if (res.data.success) {
              //window.$message.info('登录成功!');
              location.href = redirect_url;
              return;
            } else {
              window.$message.error('登录失败，' + res.data.message);
            }
          } else {
            window.$message.error('请求失败，response code' + res.status);
          }
        })
        .catch((err) => {
          window.$message.error('请求失败,' + err.message);
        });
    };

    return {
      model: modelRef,
      rules: rules,
      submit: submit
    };
  }
});
</script>

<style scoped>
.container {
  display: flex;
  width: 100%;
  height: 100%;
  background: #efefef;
}

.login_form {
  height: 250;
  width: 300;
  margin: 100px auto;
  border: 1px solid #ccc;
  border-radius: 10px;
  padding: 20px;
  background: #fff;
}
</style>