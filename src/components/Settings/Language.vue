<template>
  <div class="language">

    <div class="x-header">
      <x-header :title="$t('languageSetting')" :left-options="{preventGoBack:true, backText: ''}" @on-click-back="$router.replace({name:'Setting'})"></x-header>
    </div>

    <div class="setting-list pl-50 pr-50">
      <!--简体中文-->
      <div class="list" @click="changeLang('zhcn')">
        <span>简体中文</span>
        <span class="icon" v-if="language === 'zhcn'"></span>
      </div>

      <!--繁體中文-->
      <div class="list" @click="changeLang('zhtw')">
        <span>繁體中文</span>
        <span class="icon" v-if="language === 'zhtw'"></span>
      </div>

      <!--English-->
      <div class="list" @click="changeLang('en')">
        <span>English</span>
        <span class="icon" v-if="language === 'en'"></span>
      </div>

    </div>
  </div>
</template>

<script>
  export default {
    name: "Language",
    data() {
      return {
        language: localStorage.getItem('language')
      }
    },
    methods: {
      changeLang(lang) {
        this.language = lang
        this.$i18n.locale = lang
        localStorage.setItem('language', lang)
      }
    }
  }
</script>

<style lang="less" scoped>
  .language {
    overflow-y: auto;

    .setting-list {
      margin-top: 160px;
      margin-bottom: 30px;
      text-align: left;
      border-top: 1px solid #506FF2;
      border-bottom: 1px solid #506FF2;
      .list {
        color: @text-color-1;
        line-height: 150px;
        &:not(:last-child) {
          border-bottom: 1px solid #DFEDFF;
        }
        .icon {
          display: inline-block;
          float: right;
          margin-top: 50px;
          width: 50px;
          height: 40px;
          background-repeat: no-repeat;
          background-size: cover;
          background-image: url('../../assets/images/default/icon_basis_choice.png');
        }
      }
    }
  }
</style>
