<template>
  <div>
    <h1>ברוכים הבאים למערכת ניהול נתוני שעון נוכחות</h1>
    <div id="boxInput">
      <label for="file-upload" class="custom-file-upload">
        לחץ כאן לבחור קובץ
      </label>
    </div>
    <input id="file-upload" class="file-upload" v-if="!inLoad" type="file" @change="uploadFile" accept=".dat" />
    <router-link to="/table"  class="upload-button" v-if="!inLoad && dataInLocal">הצג טבלאות</router-link>

  </div>
  <div v-if="inLoad" id="loaders-box">
  <div class="loader" ></div>
  <div class="loader2"></div>

</div>
  <div v-if="isUploadSuccessful" class="success-message">
    הקובץ הועלה בהצלחה
  </div>
  <div id="footer">
  <hr/>
  <p>מערכת זאת נבנתה ע"י y.d. Systems</p>
</div>
</template>

<script>
import axios from 'axios';
export default {
  data() {
    return {
      msg: 'Vite + Vue',
      fileContent: '',
      isUploadSuccessful: false, // משתנה להצגת הודעה על העלאה מוצלחת
      inLoad: false, // משתנה להצגת מסך טעינה
      dataInLocal: false,
      responseData: []

    };
  },
  methods: {
    uploadFile(event) {
      this.inLoad = true;
      const file = event.target.files[0];
      const formData = new FormData();
      formData.append('file', file);

      // הוסף נתונים ל- formData
      for (let [key, value] of formData.entries()) {
        console.log(key, value);
      }
      axios
        .post(process.env.VUE_APP_SERVER + '/extractor/upload', formData, {
          headers: {
            'Content-Type': 'multipart/form-data'
          }
        })
        .then(response => {
          console.log(response); // עקוב אחרי התגובה מהשרת
          this.responseData = response.data;
          localStorage.setItem('data', JSON.stringify(response.data));  
          this.inLoad = false;  // הסתרת מסך הטעינה      
          this.isUploadSuccessful = true; // הצגת הודעה על העלאה מוצלחת
          this.dataInLocal = true; // הגדרה שקיים דאטה בלוקל סטורג

          setTimeout(() => {
            this.isUploadSuccessful = false;
          }, 6000);
        });
    },
    

  },
  mounted() {
    // בדיקה האם קיימים נתונים בלוקל סטורג
    if (localStorage.getItem('data')) {
      this.dataInLocal = true;
    }
  }
 
};
</script>

<style scoped>
h1 {
  text-align: center;
  color: rgb(82, 82, 191);
  font-size: 50px;
}

#boxInput {
  text-align: center;
  border: 2px solid #89c1f9;
  border-radius: 20px;
  display: inline-block;
  padding: 6px 12px;
  background-color: #f8f8f8;
}

input[type="file"] {
  display: none;
}

.file-upload {
  border: 1px solid #ccc;
  display: inline-block;
  padding: 6px 12px;
  cursor: pointer;
}

.upload-button {
  display: block;
  width: 200px;
  height: 50px;
  margin: 20px auto;
  background-color: blue;
  color: white;
  font-size: 18px;
  border: none;
  border-radius: 25px;
  cursor: pointer;
  transition: all 0.3s ease-in-out;
  text-decoration: none;
  text-align: center; /* טקסט במרכז אופקי */
  line-height: 50px; /* טקסט במרכז רוחבי */
}

.success-message {
  position: fixed;
  bottom: 10px;
  right: 10px;
  background-color: green;
  color: white;
  padding: 10px;
  border-radius: 5px;
  animation: fadeOut 5s forwards;
}

@keyframes fadeOut {
  0% {
    opacity: 1;
  }
  50% {
    opacity: 1;
  }
  100% {
    opacity: 0;
  }
}

#loaders-box{
  display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    margin: 25px;
}

.loader {
  width: 175px;
  height: 80px;
  display: block;
  margin: auto;
  background-image: radial-gradient(circle 25px at 25px 25px, #FFF 100%, transparent 0), radial-gradient(circle 50px at 50px 50px, #FFF 100%, transparent 0), radial-gradient(circle 25px at 25px 25px, #FFF 100%, transparent 0), linear-gradient(#FFF 50px, transparent 0);
  background-size: 50px 50px, 100px 76px, 50px 50px, 120px 40px;
  background-position: 0px 30px, 37px 0px, 122px 30px, 25px 40px;
  background-repeat: no-repeat;
  position: relative;
  box-sizing: border-box;
}
.loader::after {
  content: '';  
  left: 50%;
  bottom: 30px;
  transform: translate(-50%, 0);
  position: absolute;
  border: 15px solid transparent;
  border-bottom-color: #FF3D00;
  box-sizing: border-box;
  animation: fadePull 1s linear infinite;
}
.loader::before {
  content: '';  
  left: 50%;
  bottom: 15px;
  transform: translate(-50%, 0);
  position: absolute;
  width: 15px;
  height: 15px;
  background: #FF3D00;
  box-sizing: border-box;
  animation: fadePull 1s linear infinite;
}

@keyframes fadePull {
  0% {
    transform: translate(-50%, 15px);
    opacity: 0;
  }
  50% {
    transform: translate(-50%, 0px);
    opacity: 1;
  }
  100% {
    transform: translate(-50%, -15px);
    opacity: 0;
  }
}
.loader2 {
  width: fit-content;
  font-weight: bold;
  font-family: monospace;
  font-size: 30px;
  background: linear-gradient(135deg,#0000 calc(50% - 0.5em),#42b983 0 calc(50% + 0.5em),#0000 0) right/300% 100%;
  animation: l22 2s infinite;
}
.loader2::before {
  content: "הקובץ בטעינה...";
  color: #0000;
  padding: 0 5px;
  background: inherit;
  background-image: linear-gradient(135deg,#000 calc(50% - 0.5em),#fff 0 calc(50% + 0.5em),#000 0);
  -webkit-background-clip:text;
          background-clip:text;
}

@keyframes l22{
  100%{background-position: left}
}


#footer {
  position: fixed;
  bottom: 0;
  bottom: 0;
  left: 0;
  width: 100%;
  padding: 20px;
  text-align: center;
  font-size: 14px;
  color: #666666;
}
</style>
