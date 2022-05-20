<template>
  <div class="ak-style-injector-form">
    <div class="inputs">
      <input type="text" placeholder="css property (ie. background-color)" ref="key" required />
      <input type="text" placeholder="property value (ie. red)" ref="val" required />
      <button type="button" @click="add">ADD</button>
    </div>

    <div class="ak-style-injector-listing">
      <ul>
        <li v-for="(_, k) in model" :key="k">
          <span>{{k}}:</span><input type="text" v-model="model[k]" @input="syncStyle" /><button @click="remove(k)">X</button>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: 'AkStyleInjector',
  data: () => ({
    model: {}
  }),
  methods: {
    add () {
      this.model = { ...this.model } // stupid hack for reactivity

      this.model[this.$refs.key.value] = this.$refs.val.value

      this.syncStyle()

      this.$emit('change', this.model)

      this.$refs.key.value = ''
      this.$refs.val.value = ''
      this.$refs.key.focus()
    },
    remove (key) {
      delete this.model[key]

      this.model = { ...this.model } // stupid hack for reactivity

      this.syncStyle()

      this.$emit('change', this.model)

      this.$refs.key.focus()
    },
    syncStyle () {
      // const keys = Object.keys(this.model)
      const id = 'ak-style-injector'
      let style = document.getElementById(id)
      if (style === null) {
        style = document.createElement('style')
        style.setAttribute('id', id)
        document.getElementsByTagName('head')[0].appendChild(style)
      }

      let data = ''
      Object.keys(this.model).forEach(k => {
        data += `${k}: ${this.model[k]};`
      })
      style.innerText = `html { ${data} }`
    }
  }
}
</script>

<style scoped>
.ak-style-injector-form {
  font-size: 10px;
}
.inputs input[type="text"] {
  padding: 0.25rem 0.5rem;
  margin: 0.15rem 0.5rem;
  min-width: 15rem;
}

.inputs input[type="text"]:first-child {
  margin-left: 0;
}
.inputs input[type="text"]:last-child {
  margin-right: 0;
}

.inputs button {
  padding: 0.25rem 0.5rem;
}

.ak-style-injector-listing {
  font-size: 1.2rem;
}
</style>
