<template lang="pug">
  
  header.header
    loader-component(v-if="isLoading")
    .tags-popup.only-desktop(v-if="isTagsOpen")
      .tag-wrapper
        .tags-inner(v-for="(tags, key) in sortTags()", :key="key")
          .letter 
            | {{key.toUpperCase()}}
          .tags-wrapper
            div(v-for="(tag, id) in tags", :key="id")
              router-link.tag-name(:to="`/tag/${tag.name}`")
                | \#{{tag.name.toUpperCase()}}

    .top
      .container
        .left
          .logo(@click="closeMenu", :class="{'logo--opened': isOpen}")
            | Meda
            span ch

        .right.only-desktop
          .header__wrapper-dop
            .header__category
              router-link.category-name(to="/tag/авторская статья") АВТОРСКИЕ СТАТЬИ
            .header__category
              router-link.category-name(to="/tag/новость") НОВОСТИ
            .header__category
              router-link.category-name(to="/tag/перевод") ПЕРЕВОДЫ
            .header__category
              router-link.category-name(to="/tag/медиа") МЕДИА
            .header__serch-wrapper
              label
                form(@submit.prevent="search")
                  .header__search-icon
                  input(type="text" class="header__search" name="search" placeholder = "Поиск" v-model="query")
              .header__search-buffer
        .burger.only-mobile(@click="toggleMenu")
          #nav-icon3(:class="{open: isOpen}")
            span
            span
            span
            span

    .bottom.only-desktop
      .container
        .header__main-nav.only-desktop
          router-link(v-for="(tag, id) in popularTags" v-if="id < 7" , :to="`/tag/${tag.name}`").main-nav__item
            |{{tag.name.toUpperCase()}}
          .header__medach(@click="toggleTags")
            | еще 
    
    .menu-page(:class="{visible: isOpen}")

      .search
        label
          form(@submit.prevent="search")
            button.header__search-icon(type="submit")
            input(type="text" class="header__search" name="search" placeholder = "Поиск" v-model="query")

      .tags
        .main-nav__item(v-for="(tag, id) in popularTags" v-if="id < 8" @click="goToTag(tag.name)")
          | {{tag.name.toUpperCase()}}

      .social
        .header__socials-wrapper
          a(href="#").header__social.header__socials-vk
          a(href="#").header__social.header__socials-facebook
          a(href="#").header__social.header__socials-telegram

</template>

<script>
  import LoaderComponent from './Loader.vue'
  import {
    mapGetters
  } from 'vuex'
  
  export default {
    data() {
      return {
        query: '',
        isLoading: false,
        isOpen: false,
        isTagsOpen: false
      }
    },
  
    created() {
      this.$store.dispatch('getTagsCount')
      this.$store.dispatch('getTags')
    },
  
    methods: {
      search(e) {
        this.isLoading = true
        this.$store.dispatch('search', this.query).then(() =>
          setTimeout(() => {
            document.querySelector('#app').style.overflow = 'initial'
            this.isOpen = false
            this.$router.push('/search')
            this.isLoading = false
          })
        )
      },
  
      toggleTags() {
        this.isTagsOpen = !this.isTagsOpen
        if (this.isTagsOpen) {
          document.getElementsByTagName('body')[0].style.overflowY = 'hidden'
        } else {
          document.getElementsByTagName('body')[0].style.overflowY = 'initial'
        }
      },
  
      sortTags() {
        const tags = this.tags.reduce((res, curr) => {
          const firstLetter = curr.name[0].toLowerCase();
          if (!res.hasOwnProperty(firstLetter)) {
            res[firstLetter] = [];
          }
          res[firstLetter].push(curr)
          return res;
        }, {})
  
        const ordered = {};
        Object.keys(tags).sort().forEach(function(key) {
          ordered[key] = tags[key];
        });
  
        return ordered
      },
  
      toggleMenu() {
        this.isOpen = !this.isOpen
  
        if (this.isOpen) {
          document.querySelector('#app').style.overflow = 'hidden'
          document.querySelector('#app').style.maxHeight = '100vh'
        } else {
          document.querySelector('#app').style.overflow = 'initial'
          document.querySelector('#app').style.maxHeight = 'initial'
        }
      },
  
      closeMenu() {
        this.isOpen = false
        document.querySelector('#app').style.overflow = 'initial'
        document.querySelector('#app').style.maxHeight = 'initial'
        this.$router.push('/')
      },
  
      goToTag(tag) {
        this.isOpen = false;
        document.querySelector('#app').style.overflow = 'initial'
        document.querySelector('#app').style.maxHeight = 'initial'
        this.$router.push(`/tag/${tag}`)
      }
    },
  
    computed: {
      ...mapGetters(['popularTags', 'tags'])
    },
  
    components: {
      LoaderComponent
    }
  }
</script>

<style scoped lang="scss">
  .header {
    min-height: 62px;
    font-family: Helvetica, sans-serif;
    z-index: 10;
    &__category {
      &:not(:first-child) {
        margin-left: 35px;
      }
    }
  }
  
  .header .container {
    max-width: 1280px;
    width: 100%;
    min-height: 62px;
  }
  
  .category-name {
    font-size: 15px;
    color: #FFFFFF;
    letter-spacing: 0;
    white-space: nowrap;
  }
  
  .tags-popup {
    position: fixed;
    top: 102px;
    width: 100%;
    height: calc(100vh - 102px);
    overflow-y: auto;
    background: #fff;
    z-index: 30;
  }
  
  .tag-wrapper {
    display: flex;
    flex-flow: row nowrap;
    flex-wrap: wrap;
    max-width: 1200px;
    width: 100%;
    padding: 50px 30px 30px 30px;
    margin: 0 auto;
  }
  
  .tags-inner {
    width: 25%;
    padding: 10px 20px;
    margin-top: 30px;

    &:hover {
      z-index: 15;
    }
  }
  
  .letter {
    font-size: 22px;
  }
  
  .tag-name {
    display: inline-block;
    font-size: 14px;
    margin-top: 10px;
    max-width: 100%;
    white-space: nowrap;
    text-overflow: ellipsis;
    overflow: hidden;
    background: #fff;
    transition: max-width .3s ease;
    z-index: 10;

    &:hover {
      max-width: 700px;
      z-index: 30;
    }

  }
  
  .header__main-nav {
    position: relative;
    display: flex;
    flex-flow: row nowrap;
    justify-content: flex-start;
    overflow: hidden;
    white-space: nowrap;
    max-width: 753px;
    width: 100%;
    font-size: 10px;
  }
  
  .main-nav__item {
    position: relative;
    display: inline-block;
    white-space: nowrap;
    letter-spacing: 0.3px;
    padding: 3px 7px;
    border: 1px solid #7F7F7F;
    border-radius: 3px;
    font-family: sans-serif;
    font-size: 12px;
    color: #7F7F7F;
    letter-spacing: 0;
    text-transform: lowercase;
    &:not(:first-child) {
      margin-left: 10px;
    }
  }
  
  .top {
    background: #18164F;
  }
  
  .top .container {
    display: flex;
    flex-flow: row nowrap;
    align-items: center;
    justify-content: space-between;
  }
  
  .main-nav__item.main-nav__item::after {
    content: '';
    position: absolute;
    opacity: 0;
    left: 0;
    bottom: -2px;
    width: 100%;
    height: 1px;
    background: #505666;
  }
  
  .main-nav__item:hover.main-nav__item::after {
    opacity: 1;
  }
  
  .bottom {
    background: #ffffff;
    padding: 10px 0;
    .container {
      min-height: initial;
    }
  }
  
  .header__wrapper-dop {
    display: flex;
    justify-content: space-between;
    align-items: center;
    width: 100%;
  }
  
  .header__serch-wrapper {
    margin-left: 60px;
    position: relative;
    &::before {
      content: '';
      position: absolute;
      height: 20px;
      width: 1px;
      background: #6CF6FF;
      top: 50%;
      transform: translateY(-50%);
      left: -30px;
    }
    input {
      width: 0;
      transition: width .2s ease;
      padding-left: 10px;
      color: #ffffff;
      border: 0;
      background: transparent;
      font-size: 13px;
      &::-webkit-input-placeholder {
        /* Chrome/Opera/Safari */
        color: #fff;
      }
      &::-moz-placeholder {
        /* Firefox 19+ */
        color: #fff;
      }
      &:-ms-input-placeholder {
        /* IE 10+ */
        color: #fff;
      }
      &:-moz-placeholder {
        /* Firefox 18- */
        color: #fff;
      }
    }
    input:focus {
      width: 126px;
    }
  }
  
  .header__serch-wrapper label {
    display: flex;
    align-items: center;
  }
  
  .header__search {
    width: 52px;
    min-width: 52px;
    max-width: 140px;
    border: none;
    font-size: 10px;
    outline: none;
    min-width: 0px;
    transition: min-width .2s ease;
  }
  
  .header__search:focus {
    border-bottom: 0.5px solid #0ea4fb;
  }
  
  .header__search-buffer {
    position: absolute;
    top: -1000px;
    left: -1000px;
    visibility: hidden;
    white-space: nowrap;
    font-size: 10px;
  }
  
  .header__search-icon {
    flex-shrink: 0;
    cursor: pointer;
    display: block;
    width: 23px;
    height: 23px;
    margin-right: 8px;
    margin-left: auto;
    background: url('../static/images/search.svg') no-repeat center;
    background-size: contain;
  }
  
  .header__socials-wrapper {
    display: flex;
    justify-content: space-between;
  }
  
  .header__social {
    display: block;
    width: 15px;
    height: 14px;
    margin: 0 5px;
  }
  
  .header__socials-vk {
    background: url('../static/images/vk.svg') no-repeat center / contain;
  }
  
  .header__socials-facebook {
    background: url('../static/images/facebook.svg') no-repeat center / contain;
  }
  
  .header__socials-telegram {
    background: url('../static/images/telegram.svg') no-repeat center / contain;
  }
  
  .calendar {
    position: relative;
    font-size: 10px;
  }
  
  .calendar::before {
    content: '';
    position: absolute;
    opacity: 0;
    left: 0;
    bottom: -2px;
    width: 100%;
    height: 1px;
    background: #505666;
  }
  
  .calendar:hover::before {
    opacity: 1;
  }
  
  .header__medach {
    background-size: contain;
    margin-left: 15px;
    color: #000;
    cursor: pointer;
    white-space: nowrap;
    font-family: sans-serif;
    font-size: 13px;
    color: #7F7F7F;
    letter-spacing: 0;
    align-self: center;
  }
  
  .logo {
    z-index: 20;
    cursor: pointer;
    color: #fff;
    font-family: 'Archangelsk';
    font-size: 25px;
    transition: color .2s ease;
    color: #FFFFFF;
    letter-spacing: 2.08px;
    span {
      color: #6CF6FF;
    }

    &--opened {
      color: #7F7F7F;
    }
  }
  
  form {
    display: flex;
    width: 100%;
  }
  
  button {
    border: none;
    outline: none;
    cursor: pointer;
  }
  
  input {
    flex: 1 1 auto;
  }
  
  .left,
  .right {
    display: flex;
    flex-flow: row nowrap;
    align-items: center;
  }
  
  .only-mobile {
    display: none;
  }
  
  .burger {
    opacity: .7;
    height: 22px;
    z-index: 20;
    img {
      display: block;
      width: 100%;
      height: 100%;
    }
  }
  
  #nav-icon3 {
    width: 27px;
    height: 22px;
    position: relative;
    -webkit-transform: rotate(0deg);
    -moz-transform: rotate(0deg);
    -o-transform: rotate(0deg);
    transform: rotate(0deg);
    -webkit-transition: .5s ease-in-out;
    -moz-transition: .5s ease-in-out;
    -o-transition: .5s ease-in-out;
    transition: .5s ease-in-out;
  }
  
  #nav-icon3 span {
    display: block;
    position: absolute;
    height: 2px;
    width: 100%;
    background: #fff;
    opacity: 1;
    left: 0;
    -webkit-transform: rotate(0deg);
    -moz-transform: rotate(0deg);
    -o-transform: rotate(0deg);
    transform: rotate(0deg);
    -webkit-transition: .25s ease-in-out;
    -moz-transition: .25s ease-in-out;
    -o-transition: .25s ease-in-out;
    transition: .25s ease-in-out;
  }

  #nav-icon3.open span {
    background: #000;
  }
  
  #nav-icon3 span:nth-child(1) {
    top: 0px;
  }
  
  #nav-icon3 span:nth-child(2),
  #nav-icon3 span:nth-child(3) {
    top: 7px;
  }
  
  #nav-icon3 span:nth-child(4) {
    top: 14px;
  }
  
  #nav-icon3.open span:nth-child(1) {
    top: 7px;
    width: 0%;
    left: 50%;
  }
  
  #nav-icon3.open span:nth-child(2) {
    -webkit-transform: rotate(45deg);
    -moz-transform: rotate(45deg);
    -o-transform: rotate(45deg);
    transform: rotate(45deg);
  }
  
  #nav-icon3.open span:nth-child(3) {
    -webkit-transform: rotate(-45deg);
    -moz-transform: rotate(-45deg);
    -o-transform: rotate(-45deg);
    transform: rotate(-45deg);
  }
  
  #nav-icon3.open span:nth-child(4) {
    top: 7px;
    width: 0%;
    left: 50%;
  }
  
  .menu-page {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100vh;
    background: #fff;
    opacity: 0;
    transition: opacity .2s ease;
    will-change: opacity;
    pointer-events: none;
    z-index: 10;
    padding-top: 82px;
  }
  
  .visible {
    opacity: 1;
    pointer-events: initial;
  }
  
  .search {
    position: absolute;
    left: 180px;
    top: 31px;
    input {
      font-size: 18px;
      width: 50vw;
      max-width: initial;
    }
    button {
      transform: translateY(37%);
    }
  }
  
  .tags {
    padding: 0 30px;
    display: flex;
    flex-flow: column nowrap;
    text-align: center;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    .main-nav__item:not(:first-child) {
      margin-left: 0;
      margin-top: 30px;
    }
    .main-nav__item {
      font-size: 20px;
    }
  }
  
  .social {
    position: absolute;
    bottom: 30px;
    right: 30px;
    .header__social {
      width: 27px;
      height: 27px;
      margin: 0 10px;
    }
    .header__socials-facebook {
      height: 22px;
      margin-top: 2px;
    }
  }
  
  @media (max-width: 1024px) {
    .only-desktop {
      display: none;
    }
    .only-mobile {
      display: block;
    }
    .header {
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      background: #fff;
      box-shadow: 0px 0px 3px 0px rgba(0, 0, 0, 0.1);
      .container {
        padding: 0 40px;
      }
    }
  }
  
  @media (max-width: 767px) {
    .header .container {
      padding: 0 20px;
    }
    .search {
      left: 148px;
      max-width: 125px;
    }

    .main-nav__item {
      padding: 0;
      border: none;
    }

    .header__search-icon {
      display: none;
    }

    .search {
      top: 18px;
    }
  }
</style>

