<template>
  <v-card light>
    <div class="wrap">
      <template class="newsView">
        <v-card class="newsItem" v-for="(news, index) in dataPublish" :key="index">
<!--        TODO: - Модальное окно для редактирования новости-->
          <v-dialog v-model="dialog" fullscreen :retain-focus="false">
            <template v-slot:activator="{ on, attrs }">
              <v-btn
                small
                v-bind="attrs"
                v-on="on">
                Put
              </v-btn>
            </template>
            <v-card light>
              <template>
                <v-form class="form_imp" @submit.prevent="updateNews">
                  <v-text-field v-model="newsPublish.url" label="Метатег"></v-text-field>
                  <v-text-field v-model="newsPublish.h1" label="Заголовок"></v-text-field>
                  <v-text-field v-model="newsPublish.description" label="Описание"></v-text-field>

                  <editor
                    class="editorContext"
                    api-key="h9i301p2s1pu6e0a716zckeigcjrd7oi3mkgjzgia3y81htw"
                    v-model="newsPublish.content"
                    :init=" {
                      language: 'ru',
                      skin: 'bootstrap',
                      selector: 'textarea#default',
                      format: 'text',
                      plugins: 'charmap code insertdatetime table export',
                      toolbar: 'undo redo | formatselect bold italic underline strikethrough | fontfamily fontsize  | alignleft aligncenter alignright alignjustify | outdent indent |  numlist bullist ' +
                               '| forecolor backcolor removeformat | pagebreak | charmap emoticons | fullscreen  preview save print | insertfile image media template link anchor codesample | ltr rtl | selectall | ' +
                               'code | codesample | insertdatetime table export',
                      automatic_uploads: true,
                      forced_root_block: false,
                      }"/>
                </v-form>
              </template>
              <v-card-actions>
                <v-btn small type="submit" color="primary" class="publish_btn" width="150px">
                  Опубликовать
                </v-btn>
                <v-btn small
                       color="primary"
                       @click="dialog = false"
                >
                  Закрыть
                </v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
<!--          TODO: Конец модального окна для редактирования новости-->

          <v-btn small @click="deleteNews(news.id)" class="primary">
            Del
          </v-btn>

          <div>
            {{ news.url }}
            {{ news.h1 }}
            {{ news.description }}
            {{ news.content }}
          </div>
        </v-card>
      </template>

      <template>
        <v-form class="form_imp" @submit.prevent="publishNews">
          <v-text-field v-model="newsPublish.url" label="Метатег"></v-text-field>
          <v-text-field v-model="newsPublish.h1" label="Заголовок"></v-text-field>
          <v-text-field v-model="newsPublish.description" label="Описание"></v-text-field>

          <editor
            class="editorContext"
            api-key="h9i301p2s1pu6e0a716zckeigcjrd7oi3mkgjzgia3y81htw"
            v-model="newsPublish.content"
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
              }"/>
          <v-btn type="submit" color="primary" class="publish_btn" width="150px">
            Опубликовать
          </v-btn>

        </v-form>
      </template>
    </div>
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
      dialog: false,
      editedNews: null,
      dataPublish: "",
      newsPublish:
        [{
          h1: "",
          url: "",
          description: "",
          content: "",
        }]
    }
  },

  async mounted() {
    let {data} = await axios.get('https://localhost:7158/news/')
      .then(response => (this.dataPublish = response.data))
    console.log(this.dataPublish)
  },

  methods: {

    async publishNews() {
      let payload = {
        h1: this.newsPublish.h1,
        url: this.newsPublish.url,
        description: this.newsPublish.description,
        content: this.newsPublish.content,
      };
      console.log(payload);
      await axios.post('https://localhost:7158/news',
        payload,
        {
          headers: {
            'Content-Type': 'application/json; charset=UTF-8'
          }
        })
        .then(response => {
          console.log(response)
        })
        .catch(error => {
          console.log(error.response)
        });
    },
    async deleteNews(id) {
      await axios
        .delete(`https://localhost:7158/news/${id}`)
        .then(() => {
          let x = this.newsPublish.map(news => news.id).indexOf(id);
          this.newsPublish.splice(x, 1)
        })
    },
//TODO: не понимаю как сделать обновление(изменение) записи
    async updateNews() {
      await axios.put(`https://localhost:7158/news/${id}`, {
        h1: this.newsPublish.h1,
        url: this.newsPublish.url,
        description: this.newsPublish.description,
        content: this.newsPublish.content,
      })
        .then(response => (
          console.log(response)
        ))
      /*   await axios.get(`https://localhost:7158/news/${id}`)
           .then(response => response.data)
         await axios.put(`https://localhost:7158/news/${id}`,{
           h1: this.newsPublish.h1,
           url: this.newsPublish.url,
           description: this.newsPublish.description,
           content: this.newsPublish.content,
         })*/
    }
  }
}
</script>

<style>
.form_imp {
  padding: 5px;
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

.newsView {
  height: 650px;
  overflow-y: scroll;
}

.newsItem {
  padding: 10px;
}
</style>

