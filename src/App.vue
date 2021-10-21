<template>
    <div class="row">
        <div class="col-3">
        </div>
        <div class="col-6">
            <!-- select form -->
            <select v-model="languageId" class="form-select" style="width: 50%" aria-label="Default select example" @change="changeLanguage()">
                <option value="70" selected>Python</option>
                <option value="76">C++</option>
                <option value="62">Java</option>
            </select>
            <!-- code editor -->
            <brace style="height: 500px; margin-top: 20px"
                   :fontsize="'12px'"
                   :theme="'github'"
                   :mode="this.currentLanguage"
                   :codefolding="'markbegin'"
                   :softwrap="'free'"
                   :selectionstyle="'text'"
                   :highlightline="true">
            </brace>
            <!--            Submit and result-->
            <button class="btn btn-primary" @click="makeSubmission()">
                <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" fill="currentColor" class="bi bi-chevron-right" viewBox="0 0 16 16"><path fill-rule="evenodd" d="M4.646 1.646a.5.5 0 0 1 .708 0l6 6a.5.5 0 0 1 0 .708l-6 6a.5.5 0 0 1-.708-.708L10.293 8 4.646 2.354a.5.5 0 0 1 0-.708z"/></svg>
                Run
            </button>
            <div v-if="!isLoading" style="margin-top: 10px">
                <b>{{this.response}}</b>
            </div>
            <div v-else>
                Loading...
            </div>
        </div>
        <div class="col-3">
        </div>

    </div>

</template>

<script>
import Brace from 'vue-bulma-brace'
import axios from 'axios'
export default {
  name: 'App',
  components: {
    Brace
  },
    data() {
      return {
          isLoading: false,
          languageId: 70,
          currentLanguage: 'python',
          baseURL: "http://0.0.0.0:2358",
          languageMap: {
              70: 'python',
              76: 'cpp',
              62: 'java'
          },
          requestBody: {
              "source_code": "print \"hello in python simplecoding\"",
              "language_id": "70",
              "number_of_runs": null,
              "stdin": "Judge0",
              "expected_output": null,
              "cpu_time_limit": null,
              "cpu_extra_time": null,
              "wall_time_limit": null,
              "memory_limit": null,
              "stack_limit": null,
              "max_processes_and_or_threads": null,
              "enable_per_process_and_thread_time_limit": null,
              "enable_per_process_and_thread_memory_limit": null,
              "max_file_size": null,
              "enable_network": null
          },
          token: null,
          response: null
      }
    },
    methods: {
        waitFor3second(){
            return new Promise(resolve =>
                setTimeout(() => resolve("result"),3000)
            );
        },
        makeSubmission() {
          // submit the code
            /*
             */
            // set laoding = true
            this.isLoading = true
          // integrate axios
            console.log("submit")
            console.log('html', document.getElementsByClassName('ace_content')[0].innerText)
            this.requestBody.source_code = document.getElementsByClassName('ace_content')[0].innerText
          axios.post(`${this.baseURL}/submissions`, this.requestBody)
          .then((result)=> {
              // we should get the token here
              console.log('result', result);
              this.token = result.data.token
              console.log('token', this.token);
              // wait for 3 second
              this.waitFor3second().then(()=>{
                  // get the repose from submimisson api
                  axios.get(`${this.baseURL}/submissions/${this.token}`)
                  .then((result)=> {
                      console.log('result', result);
                      // loading complete
                      this.isLoading = false
                      this.response = result.data.stdout;
                  }).catch((err)=> {
                      this.isLoading = false
                      console.log('err', err)
                  })
              })

          }).catch((err)=> {
              console.log('err', err)
          })
        },
        // on selecting different language
        changeLanguage() {
            console.log('languageId', this.languageId)
            this.requestBody.language_id = this.languageId.toString()
            this.currentLanguage = this.languageMap[this.languageId]

            // set the inner text

            // if(this.languageId == 62) {
            //     document.getElementsByClassName('ace_content')[0].innerText =
            //         `
            //            public class Main {
            //                 public static void main(String[] args) {
            //                     System.out.println("hello, world");
            //                 }
            //             }
            //     `;
            // }


        }
    }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
