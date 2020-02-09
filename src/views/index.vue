<template>
  <div class="main-index">
    <button @click="onClickReadBtn">
      click to read nfc
    </button>
    <div v-html="message" />
    <div class="error">
      {{ error }}
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      serialNumber: '',
      records: '',
      error: '',
      reader: undefined
    }
  },
  computed: {
    message() {
      return `> Serial Number: ${this.serialNumber}<br>
              > Records: (${this.records})`
    }
  },
  beforeMount() {
    
    if (/Chrome\/(\d+\.\d+.\d+.\d+)/.test(navigator.userAgent)){
      // Let's log a warning if the sample is not supposed to execute on this
      // version of Chrome.
      if (81 > parseInt(RegExp.$1)) {
        this.error = ('Warning! Keep in mind this sample has been tested with Chrome ' + 81 + '.');
      }
    }

    if (!("NDEFReader" in window)) {
      this.error = (
        "Web NFC is not available.\n" +
        'Please make sure the "Web NFC" flag is enabled on Android.'
      )
    }
  },
  beforeDestroy() {
    this.removeListener()
  },
  methods: {
    onClickReadBtn() {
      if (typeof(this.reader) !== 'undefined') {
        this.removeListener()
      }

      // eslint-disable-next-line no-undef
      this.reader = new NDEFReader()
      this.reader.scan().then(() => {
        console.log('scan started')
        this.reader.addEventListener('error', this.onReaderError)
        this.reader.addEventListener('reading', this.onReaderReading)
      })
    },
    onReaderError() {
      this.error = 'can\'t reader nfc tag'
    },
    onReaderReading({message, serialNumber}) {
      this.serialNumber = serialNumber,
      this.records = message.records.length
      this.error = ''
      console.log(`> Serial Number: ${serialNumber}`)
      console.log(`> Message: `, message)
    },
    removeListener() {
      this.reader.removeEventListener('error', this.onReaderError),
      this.reader.removeEventListener('reading', this.onReaderReading)
    }
  }
}
</script>

<style lang="scss" scoped>
.error {
  color: #ff0000;
}
</style>