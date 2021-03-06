<template>
  <div :showDelete="showDelete" :user="user" :userId="userId" class="user-info">
    <Card :dis-hover="true" class="clearfix" style="min-height:160px">
      <div class="left">
        <label class="upload-avatar" for="useravatar">
          <Avatar :src="avatar" :style="{background: '#64b1ca',color:'#fff'}" class="user-avatar" ref="avatar" size="large">{{user.username | getAvatarText}}</Avatar>
        </label>
        <p v-if="avatar">{{user.username}}</p>
        <input @change="savePhoto" accept=".jpg, .png" id="useravatar" name="useravatar" style="position:absolute;top:-100%;left:-100%;opacity:0;" type="file">
      </div>
      <div class="right">
        <div class="detail-title">详细信息
          <i-button @click="startEdit" class="edit-btn-wrap" type="text">
            <Icon style="margin-right:5px;" type="edit"></Icon>编辑
          </i-button>
          <i-button :loading="loading" @click="deleteUser(userId)" class="edit-btn-wrap" type="text" v-if="showDelete">
            <Icon style="margin-right:5px;" type="ios-trash-outline"></Icon>删除
          </i-button>
        </div>

        <hr class="hr">

        <div class="item">
          <Icon style="margin-right:5px;" type="person"></Icon>
          <span>{{fullName}}</span>
          <Tag color="green" v-if="user.isAdmin">管理员</Tag>
          <Tag color="blue" v-if="user.noReport">免填周报</Tag>
        </div>

        <div class="item">
          <Icon style="margin-right:5px;" type="email"></Icon>
          <span>{{user.email}}</span>
          <span v-if="user.email">
            <Tag color="green" v-if="user.emailVerified">已验证</Tag>
            <i-button @click="verifyEmail" style="padding:0;" type="text" v-else>
              <Tag color="red">未验证</Tag>
            </i-button>
          </span>
        </div>

        <div class="item">
          <Icon style="margin-right:5px;" type="person-stalker"></Icon>
          <span>{{user.group.name}}</span>
        </div>
      </div>
    </Card>
  </div>
</template>

<script>
import Card from 'iview/src/components/card/';
import Avatar from 'iview/src/components/avatar';
import Tag from 'iview/src/components/tag';
import Icon from 'iview/src/components/icon';
import Button from 'iview/src/components/button';
import Message from 'iview/src/components/message';
import Modal from 'iview/src/components/modal';
import AV from 'leancloud-storage';
import api from '@/api/';

export default {
  name: 'userinfo',
  components: {
    Card,
    Avatar,
    Icon,
    Tag,
    'i-button': Button
  },
  filters: {
    getAvatarText(v) {
      return v.substr(-2);
    }
  },
  props: {
    user: Object,
    userId: String,
    showDelete: Boolean
  },
  computed: {
    fullName() {
      if (!this.user.extInfo) return this.user.username;

      return this.user.username + '(' + this.user.extInfo + ')';
    }
  },
  mounted() {
    this.avatar = localStorage.getItem('userAvatar_' + this.userId);
    let span = this.$refs.avatar.$el.getElementsByTagName('span')[0];
    if (span && parseInt(span.style.lineHeight, 10) != 80) {
      span.style.lineHeight = '80px';
    }
  },
  data() {
    return {
      avatar: '',
      loading: false
    };
  },
  methods: {
    startEdit() {
      this.$emit('startEdit', this.user, this.userId);
    },
    deleteUser(userId) {
      Modal.confirm({
        title: '危险警报',
        content: '确定要删除此用户？删除后无法恢复！',
        loading: true,
        onOk: () => {
          // alert('删除' + userId);
          this._deleteUser(userId);
        }
      });
    },
    _deleteUser(userId) {
      this.loading = true;
      api
        .deleteUser(userId)
        .then(() => {
          Modal.remove();
          this.loading = false;
          Message.success({
            content: '操作成功'
          });
          this.$el.remove();
        })
        .catch(err => {
          this.loading = false;
          Message.error({
            content: '操作失败' + (err.message || err)
          });
        });
    },
    verifyEmail() {
      // 只有自己才能验证邮件
      if (this.userId != AV.User.current().id) return;

      AV.User.requestEmailVerify(this.user.email).then(() => {
        Message.info({
          content: '验证邮件已经发送至 ' + this.user.email,
          closable: true,
          duration: 3
        });
      });
    },
    savePhoto(event) {
      let file = event.target.files[0];
      let reader = new FileReader();
      reader.onload = e => {
        localStorage.setItem('userAvatar_' + this.userId, e.target.result);
        this.avatar = e.target.result;
      };
      reader.readAsDataURL(file);
    }
  }
};
</script>

<style scoped>
.user-info {
  padding: 10px;
}
.left {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  text-align: center;
  overflow: hidden;
}
.right {
  margin-left: 100px;
}
.hr {
  margin-top: 5px;
  margin-bottom: 10px;
}
.detail-title,
.item {
  padding-left: 10px;
  line-height: 28px;
  height: 28px;
}
.edit-btn-wrap {
  /* display: block; */
}
.user-avatar {
  width: 80px;
  height: 80px;

  line-height: 80px;

  border-radius: 40px;
}
.upload-avatar {
  position: relative;

  overflow: hidden;

  width: 80px;
  height: 80px;
  margin: 0;

  border-radius: 50%;
}
.upload-avatar::after {
  position: absolute;
  bottom: 0;
  left: 0;
  padding: 3px 0 5px;

  display: block;

  width: 100%;

  font-size: 12px;

  content: '更换头像';

  color: #ccc;
  background: rgba(0, 0, 0, 0.3);

  opacity: 0;
  cursor: pointer;
  transition: opacity 0.2s linear;
}
.upload-avatar:hover::after {
  opacity: 1;
}
</style>
