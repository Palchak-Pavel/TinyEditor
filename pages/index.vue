<template>
  <v-card light>
    <div class="wrap">

      <v-card class="pa-2 mb-2" elevation="0">
        <v-text-field label="Метатег"></v-text-field>
        <v-text-field label="Заголовок"></v-text-field>
        <v-text-field label="Описание"></v-text-field>
      </v-card>

      <editor
        v-model="publishContent"
        class="editorContext"
        api-key="h9i301p2s1pu6e0a716zckeigcjrd7oi3mkgjzgia3y81htw"
        :init=" {
        language: 'ru',
        skin: 'bootstrap',
        selector: 'textarea#default',
        format: 'text',
        /*block_formats: 'Paragraph=p;Heading 2=h2;Heading 3=h3;Heading 4=h4;',*/
       /* plugins: [
           'a11ychecker','advlist','advcode','advtable','autolink','checklist','export',
           'lists','link','image','charmap','preview','anchor','searchreplace','visualblocks',
           'powerpaste','fullscreen','formatpainter','insertdatetime','media','table','help','wordcount'
         ],*/
      plugins: 'charmap code insertdatetime table export',
       /*'code blocks preview powerpaste casechange importcss tinydrive searchreplace autolink autosave save directionality advcode visualblocks visualchars fullscreen image link media mediaembed template codesample table charmap pagebreak nonbreaking anchor' +
          ' insertdatetime advlist lists checklist wordcount tinymcespellchecker a11ychecker help formatpainter pageembed charmap mentions quickbars linkchecker emoticons advtable',*/
        toolbar: 'undo redo | formatselect bold italic underline strikethrough | fontfamily fontsize  | alignleft aligncenter alignright alignjustify | outdent indent |  numlist bullist ' +
          '| forecolor backcolor removeformat | pagebreak | charmap emoticons | fullscreen  preview save print | insertfile image media template link anchor codesample | ltr rtl | selectall | ' +
           'code | codesample | insertdatetime table export',

        automatic_uploads: true,
        forced_root_block: false,
      }"
      />

      <v-btn @click="publishNews" color="primary" class="publish_btn" width="150px">
        Опубликовать
      </v-btn>
    </div>

    <template v-if="publishContent">
      <div v-for="(publishContent, index) in news" :key="index" class="content_text pa-1 mb-3">
        <div>
          {{ publishContent.publish }}

          <div class="newsManagement">
            <v-btn @click="deleteNews(index)"
                   elevation="1"
                   class="newsManagementBtn mx-2 mb-2"
                   fab
                   x-small
                   color="pink">
              <v-icon color="white" dark>
                mdi-delete
              </v-icon>
            </v-btn>
            <v-btn
              @click="editNews(index)"
              elevation="1"
              class="mx-2 mb-2"
              fab
              dark
              x-small
              color="primary"
            >
              <v-icon dark>
                mdi-pencil
              </v-icon>
            </v-btn>
          </div>
        </div>
      </div>
    </template>
  </v-card>
</template>


<script>
import Editor from "@tinymce/tinymce-vue";
import axios from 'axios';

export default {
  components: {
    'editor': Editor
  },
  name: 'IndexPage',

  data() {
    return {
      publishContent: "",

      editedNews: null,
      info: null,
      news: [
        {
          publish: "",
        }
      ],
    }
  },

  async mounted() {
    /*const axios = require('axios');*/
    // TODO: я не против такой реализации, но async/await куда лучше и читабельней на мой взгляд
    /*let result = axios.get('https://localhost:7158/news/')
      .then(response => (this.info = response));*/

    // Вот такая конструкция сразу достает из ответа полезную нагрузку
    let {data} = await axios.get('https://localhost:7158/news/');
    console.log(data)
  },

  methods: {
    publishNews() {
      //if(this.publishContent.length === 0) return;
      if (this.editedNews === null) {
        this.news.push({
          publish: this.publishContent,
        })
      } else {
        this.news[this.editedNews].publish = this.publishContent;
        this.editedNews = null;
      }
    },
    editNews(index) {
      this.publishContent = this.news[index].publish;
      this.editedNews = index;
    },
    deleteNews(index) {
      this.news.splice(index, 1);
    }
  }
}
</script>

<style>

.content_text {
  border: 1px darkorange solid;
  background-color: antiquewhite;
  border-radius: 5px;
  height: 100%;
  width: 100%;
}

.wrap {
  display: flex;
  flex-direction: column;
}

.editorContext {
  height: 400px;
}

.publish_btn {
  display: block;
  margin-top: 10px;
  margin-bottom: 10px;
}

.newsManagement {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
}
</style>

