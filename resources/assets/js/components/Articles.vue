<template>
   <div class="container"  >
       <h1 style="font-family:raleway;letter-spacing: 10px;margin:50px 250px 50px 250px;
        " class="text-dark"> Articles Using API </h1>

       <nav aria-label="Page navigation example">
            <ul class="pagination">
                <li class="page-item" v-bind:class="[{disabled: !pagination.prev}]">
                    <a class="page-link" href="#" @click="fetchArticles(pagination.prev)">Previous</a></li>
                    <li class="page-item disabled text-dark">
                        <a class="page-link" href="#">Page {{ pagination.current_page }} of {{ pagination.last_page }} </a>
                    </li>
                <li class="page-item" v-bind:class="[{disabled: !pagination.next}]">
                    <a class="page-link" href="#" @click="fetchArticles(pagination.next)">Next</a></li>
            </ul>
        </nav>

        <div class="container card gr">
                <h2 class="card-title mt-4" style="font-family: raleway;letter-spacing: 5px;font-weight: 700;" align="center">Create New Article</h2>
                <form class="form-group card-body" @submit.prevent="newArticle()" >
                    <input class="form-control mb-4" type="text" v-model="article.title" placeholder="Title Goes Here....">
                    <input class="form-control mb-4" type="text" v-model="article.body" placeholder="And the body here....">
                    <input type="submit" class="form-control btn btn-outline-danger" value="Add Article">
                </form>
            </div>

      <div class="card mt-2 text-capitalize text-center border-warning  "   v-for="article in articles" id="card" v-bind:key="article.id" style="margin-bottom: 5%;">
         <div class="card-header border-secondary bg-dark text-warning pull-right">
            {{ article.title }}
         </div>
         <div class="card-body bg-secondary border-warning text-warning">
            {{ article.body }}
         </div>
        <div class="card-footer bg-warning text-primary border-warning ">
            <button class="btn btn-outline-danger btn-block text-danger pull-right" @click="editArticle(article)" >Edit</button>     
        </div>
        <div class="card-footer bg-danger text-primary border-warning">
               <button @click="deleteArticle(article.id)" class="btn btn-outline-warning btn-block">Delete</button>
        </div> 
    </div>
      <footer class="footer footer-primary bg-warning text-warning" style="margin-left:-10%;margin-right: -9%;">
      <div class="container" align="center" style="padding:25px 25px 25px 25px;" >
        <span class="text-muted " >&copy; Rupesh Chadhari</span>
      </div>
    </footer>
      </div>
</template>

<script>
    export default {
        data() {
            return {
                articles: [],
                article: {
                    id: '',
                    title: '',
                    body: ''
                },
                article_id: '',
                pagination: {},
                edit: false
            }
        },
        created() {
            this.fetchArticles();
            var welcome = new Audio('hello.mp3');
            welcome.play();
        },
        methods: {
            fetchArticles(page_url) {
                let vm = this;
                page_url = page_url || 'api/articles';
                fetch(page_url)
                    .then(result => result.json())
                    .then(result => {
                        this.articles = result.data;
                        vm.makePagination(result.links, result.meta);
                    })
                    .catch(err => console.log(err));
            },
            newArticle() {

                if (this.edit === false) {
                    fetch('api/article', {
                            method: 'post',
                            body: JSON.stringify(this.article),
                            headers: {
                                'content-type': 'application/json'
                            }
                        }).then(result => result.json())
                        .then(data => {
                            this.article.title = "";
                            this.article.body = "";
                            alert("Article Added");
                            this.fetchArticles();
                        })
                    var sou = new Audio('notify.mp3');
                    sou.play();
                } else {
                    fetch('api/article', {
                            method: 'put',
                            body: JSON.stringify(this.article),
                            headers: {
                                'content-type': 'application/json'
                            }
                        }).then(result => result.json())
                        .then(data => {
                            this.article.title = "";
                            this.article.body = "";
                            alert("Article Updated");
                            this.edit = false;
                            this.fetchArticles();
                        })
                    var sou = new Audio('notify.mp3');
                    sou.play();
                }
            },
            editArticle(article) {
                this.edit = true;
                this.article.id = article.id;
                this.article.article_id = article.id;
                this.article.title = article.title;
                this.article.body = article.body;

            },
            makePagination(link, meta) {
                let pagination = {
                    current_page: meta.current_page,
                    last_page: meta.last_page,
                    prev: link.prev,
                    next: link.next
                }
                this.pagination = pagination;

            },
            deleteArticle(id) {
                if (confirm("Are You Sure?")) {
                    var note = new Audio('notify.mp3');
                    note.play();
                    fetch(`api/article/${id}`, {
                            method: 'delete'
                        })
                        .then(r => r.json())
                        .then(r => {
                            alert("Article Deleted");
                            this.fetchArticles();
                        })
                        .catch(err => console.log(err));
                }
            }
        }
    }
</script>

<style scoped>
    .gr {
        background: linear-gradient( red, transparent), linear-gradient( -45deg, rgb(13, 243, 5), transparent), linear-gradient( 45deg, yellow, transparent);
        background-blend-mode: color-dodge;
    }
</style>