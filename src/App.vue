<template>
  <section class="is-fullheight">
    <div class="columns is-marginless is-gapless is-desktop">
      <div class="column is-4-desktop is-code-background" :class="{'is-hidden-touch': toggleView}">
        <div class="overflow-scroll-60">
          <codemirror :code="inputString" :options="editorOption"></codemirror>
        </div>
      </div>

      <div class="column is-4-desktop" :class="{'is-hidden-touch': !toggleView}">
        <div class="overflow-scroll-60" @scroll="handleScroll">
          <div class="content is-medium">
            <h1 class="title is-1">JavaScript Array Playground</h1>
            <hr>
            <contents :contents="contents" :run="run"></contents>
            <content-footer :footerEmoji="footerEmoji"></content-footer>
          </div>
        </div>
      </div>

      <div class="column is-4-desktop is-code-background">
        <div class="overflow-scroll-40">
          <codemirror class="is-code-background" :code="resultString" :options="editorOption"></codemirror>
        </div>
      </div>
    </div>
    <div class="toggle-button is-hidden-desktop is-unselectable" @click="toggleView = !toggleView">
      <span v-if="isTop && toggleView">Show Input</span>
      <i v-if="!isTop && toggleView" class="fa fa-code" aria-hidden="true"></i>
      <i v-if="!toggleView" class="fa fa-book" aria-hidden="true"></i>
    </div>
  </section>
</template>

<script>
import Contents from './components/Contents'
import ContentFooter from './components/ContentFooter'
import contents from './data/contents'
import emojis from './data/emojis'
import users from './data/users'
import products from './data/products'
import { codemirror } from 'vue-codemirror'

export default {
  data () {
    return {
      isTop: true,
      toggleView: false,
      emojisIndex: 0,
      emojis: [...emojis],
      contents: [...contents],
      users: [...users],
      products: [...products],
      result: 'output',
      scrollArea: 'TOP',
      editorOption: {
        tabSize: 2,
        mode: 'text/javascript',
        theme: 'material',
        lineNumbers: true,
        line: true,
        readOnly: true
      }
    }
  },
  computed: {
    inputString () {
      return this.emojisString + '\n' + this.usersString + '\n' + this.productsString
    },
    emojisString () {
      return 'var emojis = ' + JSON.stringify(this.emojis, null, '  ')
    },
    usersString () {
      return 'var users = ' + JSON.stringify(this.users, null, '  ')
    },
    productsString () {
      return 'var products = ' + JSON.stringify(this.products, null, '  ')
    },
    resultString () {
      return JSON.stringify(this.result, null, '  ')
    },
    footerEmoji () {
      return this.emojis[this.emojisIndex]
    }
  },
  methods: {
    run (code) {
      try {
        /*eslint-disable */
        let result = eval('this.'+ code)
        /*eslint-enable */
        if (result) {
          this.result = result
        } else {
          this.result = 'undefined'
        }
      } catch (e) {
        this.result = e.toString()
      }
    },
    handleScroll (e) {
      if (e.target.scrollTop <= 5) {
        this.isTop = true
      } else {
        this.isTop = false
      }

      let scrollArea = 'TOP'
      if (e.target.scrollHeight - (e.target.scrollTop + e.target.offsetHeight) < 80) {
        scrollArea = 'BOTTOM'
      }
      if (this.scrollArea !== scrollArea && scrollArea === 'TOP') {
        this.emojisIndex = this.randomNewValue(this.emojis.length, this.emojisIndex)
      }
      this.scrollArea = scrollArea
    },
    randomNewValue (length, oldValue) {
      let value = Math.floor(Math.random() * length)
      if (value === oldValue) {
        return this.randomNewValue(length, oldValue)
      } else {
        return value
      }
    }
  },
  components: {
    Contents,
    ContentFooter,
    codemirror
  }
}
</script>

<style lang="scss">
$column-gap: 0px;
@import '~bulma';

.CodeMirror {
  height: auto !important;
}
.toggle-button {
  display: block;
  position: fixed;
  right: 10px;
  top: 10px;
  border-radius: 3px;
  text-align: center;
  background: rgba(100, 100, 100, 0.2);
  padding: 2px 8px;
  cursor: pointer;
  z-index: 2000;
}
.toggle-button:hover {
  background: rgba(100, 100, 100, 0.5);
}

*:focus {
  outline: none;
}
.overflow-scroll-60, .overflow-scroll-40 {
  border-top: 1px solid #EEE;
  -webkit-overflow-scrolling: touch;
  overflow-y: scroll;
}
.overflow-scroll-60{
  height: 60vh !important;
  max-height: 60vh !important;
}
.overflow-scroll-40{
  height: 40vh !important;
  max-height: 40vh !important;
}
@media screen and (min-width: 980px) {
  .overflow-scroll-60, .overflow-scroll-40 {
    border-top: 0px;
    height: 100vh !important;
    max-height: 100vh !important;
  }
}
.is-code-background {
  background-color: #263238;
}
.content {
  padding: 10px 20px;
}
.round {
  border-radius: 3px;
}
.vue-icon {
  vertical-align: sub;
  height: 20px;
}
</style>
