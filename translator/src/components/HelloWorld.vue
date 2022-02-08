<template>
  <div style="width:100%; height:100vh; background-color:#81baef; padding:100px 50px">
    <a-modal :visible="visible" :centered="true" :footer="null" @cancel="onClose" :width="700">
      <h3>ADD NEW WORD</h3>
      <a-form-model ref="newWord" :model="newWord">
        <a-row :gutter="[16,16]">
          <a-col :span="24">
            <a-form-model-item label="Translation Type" prop="translationType" required :rules="req('Please choose a type')">
              <a-select v-model="newWord.translationType" size="large">
                <a-select-option value="cree">CREE</a-select-option>
                <a-select-option value="objiway">OBJIBWE</a-select-option>
                <a-select-option value="blackfoot">BLACKFOOT</a-select-option>
              </a-select>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="New Word" prop="translation" required :rules="req('Please enter the New Word')">
              <a-textarea placeholder="Enter the New Word" v-model="newWord.translation"/>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="English Translation" prop="eng" required :rules="req('Please enter the English Translation')">
              <a-textarea placeholder="Enter English Translation" v-model="newWord.eng"/>
            </a-form-model-item>
          </a-col>
        </a-row>
      </a-form-model>
      <div style="display:flex; justify-content:flex-end">
        <a-button @click="postNewWord" style="margin-right:10px; width:150px" size="large">ADD</a-button>
        <a-button size="large" :width="150" @click="onClose">CANCEL</a-button>
      </div>
      <template v-if="errorText != ''">
        <div style="color:#ea1537">{{errorText}}</div>
      </template>
    </a-modal>
    <div style="display:flex; justify-content:space-between">
      <div style="width:400px">
        <a-select style="width:400px" v-model="from" size="large">
          <a-select-option value="eng">ENGLISH</a-select-option>
          <a-select-option value="cree">CREE</a-select-option>
          <a-select-option value="objiway">OBJIBWE</a-select-option>
          <a-select-option value="blackfoot">BLACKFOOT</a-select-option>
        </a-select>
        <a-textarea v-model="userInput" style="width:400px; margin-top:20px" size="large" />
      </div>
      <a-icon @click="swap" type="swap" style="font-size:35px; cursor:pointer" />
      <div style="width:400px">
        <a-select style="width:400px" v-model="to" size="large">
          <a-select-option value="eng">ENGLISH</a-select-option>
          <a-select-option value="cree">CREE</a-select-option>
          <a-select-option value="objiway">OBJIBWE</a-select-option>
          <a-select-option value="blackfoot">BLACKFOOT</a-select-option>
        </a-select>
        <a-textarea v-model="translatedWord" style="width:400px; margin-top:20px" size="large" />
      </div>
    </div>
    <div  style="color:#ea1537" v-if="errorTranslate != ''">{{errorTranslate}}</div>
    <div style="margin-top:50px; display:flex; justify-content:center">
      <a-button @click="translate" size="large">TRANSLATE</a-button>
      <a-button @click="addNew" style="margin-left:10px" size="large">ADD NEW WORD</a-button>
    </div>
    <div @click="keyboard = !keyboard" style="display:flex; justify-content:flex-end">
      <svg style="cursor:pointer" aria-hidden="true" width="40" height="40" focusable="false" data-prefix="fas" data-icon="keyboard" class="svg-inline--fa fa-keyboard fa-w-18" role="img" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 576 512"><path fill="currentColor" d="M528 448H48c-26.51 0-48-21.49-48-48V112c0-26.51 21.49-48 48-48h480c26.51 0 48 21.49 48 48v288c0 26.51-21.49 48-48 48zM128 180v-40c0-6.627-5.373-12-12-12H76c-6.627 0-12 5.373-12 12v40c0 6.627 5.373 12 12 12h40c6.627 0 12-5.373 12-12zm96 0v-40c0-6.627-5.373-12-12-12h-40c-6.627 0-12 5.373-12 12v40c0 6.627 5.373 12 12 12h40c6.627 0 12-5.373 12-12zm96 0v-40c0-6.627-5.373-12-12-12h-40c-6.627 0-12 5.373-12 12v40c0 6.627 5.373 12 12 12h40c6.627 0 12-5.373 12-12zm96 0v-40c0-6.627-5.373-12-12-12h-40c-6.627 0-12 5.373-12 12v40c0 6.627 5.373 12 12 12h40c6.627 0 12-5.373 12-12zm96 0v-40c0-6.627-5.373-12-12-12h-40c-6.627 0-12 5.373-12 12v40c0 6.627 5.373 12 12 12h40c6.627 0 12-5.373 12-12zm-336 96v-40c0-6.627-5.373-12-12-12h-40c-6.627 0-12 5.373-12 12v40c0 6.627 5.373 12 12 12h40c6.627 0 12-5.373 12-12zm96 0v-40c0-6.627-5.373-12-12-12h-40c-6.627 0-12 5.373-12 12v40c0 6.627 5.373 12 12 12h40c6.627 0 12-5.373 12-12zm96 0v-40c0-6.627-5.373-12-12-12h-40c-6.627 0-12 5.373-12 12v40c0 6.627 5.373 12 12 12h40c6.627 0 12-5.373 12-12zm96 0v-40c0-6.627-5.373-12-12-12h-40c-6.627 0-12 5.373-12 12v40c0 6.627 5.373 12 12 12h40c6.627 0 12-5.373 12-12zm-336 96v-40c0-6.627-5.373-12-12-12H76c-6.627 0-12 5.373-12 12v40c0 6.627 5.373 12 12 12h40c6.627 0 12-5.373 12-12zm288 0v-40c0-6.627-5.373-12-12-12H172c-6.627 0-12 5.373-12 12v40c0 6.627 5.373 12 12 12h232c6.627 0 12-5.373 12-12zm96 0v-40c0-6.627-5.373-12-12-12h-40c-6.627 0-12 5.373-12 12v40c0 6.627 5.373 12 12 12h40c6.627 0 12-5.373 12-12z"></path></svg>
    </div>
    <div v-if="keyboard" style="background-color:#FFF; margin-top: 30px; display:flex; padding:10px">
      <div style="margin-right:10px; width:150px">
        <a-select style="width:150px" v-model="keyboardType" size="large">
          <a-select-option value="eng">ENGLISH</a-select-option>
          <a-select-option value="cree">CREE</a-select-option>
          <a-select-option value="objiway">OBJIBWE</a-select-option>
          <a-select-option value="blackfoot">BLACKFOOT</a-select-option>
        </a-select>
      </div>
      <div style="flex:1; display:flex">
        <div style="display:flex; justify-content:flex-end; width:100%">
          <div v-if="keyboardType == 'eng'">
            <div>
              <div style="display:flex">
                <div @click="input(letter)" class="keyboard" style="padding:5px; border:1px solid #000; width:70px; height:70px; display:flex; align-items:center" v-for="(letter, letterI) in engRow1" :key="letter+letterI"><div style="width:100%; text-align:center">{{letter}}</div></div>
              </div>
              <div style="width:100%; justify-content:center; display:flex">
                  <div style="display:flex">
                    <div @click="input(letter)" class="keyboard" style="padding:5px; border:1px solid #000; width:70px; height:70px; display:flex; align-items:center" v-for="(letter, letterI) in engRow2" :key="letter+letterI"><div style="width:100%; text-align:center">{{letter}}</div></div>
                  </div>
              </div>
              <div style="width:100%; justify-content:center; display:flex">
                  <div style="display:flex">
                    <div @click="input(letter)" class="keyboard" style="padding:5px; border:1px solid #000; width:70px; height:70px; display:flex; align-items:center" v-for="(letter, letterI) in engRow3" :key="letter+letterI"><div style="width:100%; text-align:center">{{letter}}</div></div>
                  </div>
              </div>
              <div class="keyboard" style="width:700px; border:2px solid #000; height:70px; margin-top:10px;display:flex; align-items:center"><div style="text-align:center; width:100%">SPACE</div></div>
            </div>
          </div>
          <div v-else-if="keyboardType == 'cree'">
            <div>
              <div style="width:100%; display:flex; justify-content:center">
                <div style="display:flex">
                  <div @click="symbolInput(letter)" class="symbol-keyboard" v-for="(letter, letterI) in creeRow1" :key="letter + letterI" style="border:1px solid #000">
                    <img style="width:70px; height:70px; object-fit:contain" :src="require(`@/assets/cree/${letter}.jpg`)" />
                  </div>
                </div>
              </div>
              <div style="width:100%; display:flex; justify-content:center">
                <div style="display:flex">
                  <div class="symbol-keyboard" @click="symbolInput(letter)" v-for="(letter, letterI) in creeRow2" :key="letter + letterI" style="border:1px solid #000">
                    <img style="width:70px; height:70px; object-fit:contain" :src="require(`@/assets/cree/${letter}.jpg`)" />
                  </div>
                </div>
              </div>
              <div style="width:100%; display:flex; justify-content:center">
                <div style="display:flex">
                  <div class="symbol-keyboard" @click="symbolInput(letter)" v-for="(letter, letterI) in creeRow3" :key="letter + letterI" style="border:1px solid #000">
                    <img style="width:70px; height:70px; object-fit:contain" :src="require(`@/assets/cree/${letter}.jpg`)" />
                  </div>
                </div>
              </div>
              <div class="keyboard" style="width:700px; border:2px solid #000; height:70px; margin-top:10px;display:flex; align-items:center"><div style="text-align:center; width:100%">SPACE</div></div>
            </div>
          </div>
          <div v-else-if="keyboardType == 'objiway'">
            <div>
              <div style="width:100%; display:flex; justify-content:center">
                <div style="display:flex">
                  <div class="symbol-keyboard" @click="symbolInput(letter)" v-for="(letter, letterI) in objibwayRow1" :key="letter + letterI" style="border:1px solid #000">
                    <img style="width:70px; height:70px; object-fit:contain" :src="require(`@/assets/ojibwe/${letter}.jpg`)" />
                  </div>
                </div>
              </div>
              <div style="width:100%; display:flex; justify-content:center">
                <div style="display:flex">
                  <div class="symbol-keyboard" @click="symbolInput(letter)" v-for="(letter, letterI) in objibwayRow2" :key="letter + letterI" style="border:1px solid #000">
                    <img style="width:70px; height:70px; object-fit:contain" :src="require(`@/assets/ojibwe/${letter}.jpg`)" />
                  </div>
                </div>
              </div>
              <div style="width:100%; display:flex; justify-content:center">
                <div style="display:flex">
                  <div class="symbol-keyboard" @click="symbolInput(letter)" v-for="(letter, letterI) in objibwayRow3" :key="letter + letterI" style="border:1px solid #000">
                    <img style="width:70px; height:70px; object-fit:contain" :src="require(`@/assets/ojibwe/${letter}.jpg`)" />
                  </div>
                </div>
              </div>
              <div class="keyboard" style="width:700px; border:2px solid #000; height:70px; margin-top:10px;display:flex; align-items:center"><div style="text-align:center; width:100%">SPACE</div></div>
            </div>
          </div>
          <div v-else-if="keyboardType == 'blackfoot'">
            <div>
              <div style="display:flex">
                <div class="symbol-keyboard" @click="symbolInput(letter)" v-for="(letter, letterI) in blackfootRow1" :key="letter + letterI" style="border:1px solid #000">
                  <img style="width:70px; height:70px; object-fit:contain" :src="require(`@/assets/blackfoot/${letter}.jpg`)" />
                </div>
              </div>
              <div style="width:100%; display:flex; justify-content:center">
                <div style="display:flex">
                  <div class="symbol-keyboard" @click="symbolInput(letter)" v-for="(letter, letterI) in blackfootRow2" :key="letter + letterI" style="border:1px solid #000">
                    <img style="width:70px; height:70px; object-fit:contain" :src="require(`@/assets/blackfoot/${letter}.jpg`)" />
                  </div>
                </div>
              </div>
              <div style="width:100%; display:flex; justify-content:center">
                <div style="display:flex">
                  <div class="symbol-keyboard" @click="symbolInput(letter)" v-for="(letter, letterI) in blackfootRow3" :key="letter + letterI" style="border:1px solid #000">
                    <img style="width:70px; height:70px; object-fit:contain" :src="require(`@/assets/blackfoot/${letter}.jpg`)" />
                  </div>
                </div>
              </div>
              <div class="keyboard" style="width:700px; border:2px solid #000; height:70px; margin-top:10px;display:flex; align-items:center"><div style="text-align:center; width:100%">SPACE</div></div>
            </div>
          </div>
        </div>
        <div style="margin-left:10px; width:100px">
          <div @click="input('del')" class="keyboard" style='height:70px; padding:10px; border-bottom:1px solid #000; border-top:2px solid #000; border-left:2px solid #000; border-right:2px solid #000; display:flex; align-items:center;'><div style="width:100%; text-align:center">DELETE</div></div>
          <div @click="translate" class="keyboard" style="height:140px; padding:10px; border-top:1px solid #000; border-bottom:2px solid #000; border-left:2px solid #000; border-right:2px solid #000; display:flex; align-items:center;"><div style="width:100%; text-align:center">ENTER</div></div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data() {
    return {
      from:'eng',
      to:'eng',
      keyboard:false,
      engRow1:['Q','W','E','R','T','Y','U','I','O','P'],
      engRow2:['A','S','D','F','G','H','J','K','L'],
      engRow3:['Z','X','C','V','B','N','M'],
      blackfootRow1:['-w-','wa','-w','e','-t','ta','ya','-y','-y-','i'],
      blackfootRow2:['o','pa','-p','a','sa','-s','-m','-h','-s-','ka'],
      blackfootRow3:['-k','-x','na','ma'],
      creeRow1:['ow','e','te','ye','i','o','pe'],
      creeRow2:['a','se','h','ke','n','k','t','m'],
      creeRow3:['w','s','che','ne','me'],
      objibwayRow1:['w','e','te','ye','wi','wo','pe','chi'],
      objibwayRow2:['a','se','h','ke','nii','ki','mi'],
      objibwayRow3:['y','i','si','shi','che','ne','me'],
      userInput:'',
      keyboardType:'eng',
      visible:false,
      newWord:{
        eng:'',
        translationType:'',
        translation:'',
      },
      translatedWord:'',
      errorText:'',
      errorTranslate:''
    }
  },
  methods:{
    translate() {
      if (this.from != 'eng') {
        this.$http.get(`https://translateengine-287f1-default-rtdb.firebaseio.com/${this.from}/${this.userInput}.json`).then(({data}) => {
          console.log('data NOT ENG =>>>>', data)
          this.translatedWord = data
          if (data == null) {
            this.errorTranslate = 'This word does not exist in the database. Add it to translate!'
          } else {
            this.errorTranslate = ''
          }
        })
      } else {
        this.userInput = this.userInput.toLowerCase()
        this.$http.get(`https://translateengine-287f1-default-rtdb.firebaseio.com/${this.from}/eng-${this.userInput}-to${this.to}.json`).then(({data}) => {
          this.translatedWord = data
          console.log('data FROM ENG ==>>>', data)
          if (data == null) {
            this.errorTranslate = 'This word does not exist in the database. Add it to translate!'
          } else {
            this.errorTranslate = ''
          }
        })
      }
    },
    swap() {
      let temp1 = this.from
      let temp2 = this.to
      this.to = temp1
      this.from = temp2
    },
    symbolInput(letter) {
      console.log('letter', letter)
      if(letter[0] == '-') letter = letter.substring(1, letter.length - 1)
      if(letter[letter.length - 1] == '-') letter = letter.slice(0,-1)
      this.userInput = this.userInput + letter
    },
    input(letter) {
      if(letter != 'del') {
        this.userInput = this.userInput + letter
      } else if (letter == 'del') {
        this.userInput = this.userInput.slice(0, -1)
      }
    },
    addNew() {
      this.visible = true
    },
    onClose() {
      this.visible = false
      this.$refs.newWord.resetFields()
      this.errorText = ''
    },
    req:msg=>({required:true,message:msg}),
    postNewWord() {
      this.$refs.newWord.validate(valid => {
        if (valid) {
          let addValid = false
          this.$http.get(`https://translateengine-287f1-default-rtdb.firebaseio.com/${this.newWord.translationType}/${this.newWord.translation}.json`).then(({data}) => {
            console.log('THIS IS GET DATA', data)
            if (data == null) addValid = true
            if (addValid) {
              console.log('run this')
              this.$http.post('https://translateengine-287f1-default-rtdb.firebaseio.com/posts.json', this.newWord).then(({data}) => {
                console.log('dataaaaa =>>>', data)

                let sendObj = {}
                sendObj[this.newWord.translation] = this.newWord.eng

                let engObj = {}
                engObj[`eng-${this.newWord.eng}-to${this.newWord.translationType}`] = this.newWord.translation
                this.$http.patch(`https://translateengine-287f1-default-rtdb.firebaseio.com/${this.newWord.translationType}.json`, sendObj)
                this.$http.patch(`https://translateengine-287f1-default-rtdb.firebaseio.com/eng.json`, engObj)
                this.onClose()
                if (this.errorText != '') this.errorText = ''
              })
            } else {
              this.errorText = 'This word already existed.'
            }
          }) 
        }
      })
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.keyboard:hover{
  background-color:#000;
  cursor:pointer;
  color:#FFF;
}
.keyboard{
  font-weight:600;
  font-size:20px;
}
.symbol-keyboard:hover{
  border:1px solid rgb(129, 186, 239) !important;
}
.symbol-keyboard{
  cursor: pointer;
}
</style>
