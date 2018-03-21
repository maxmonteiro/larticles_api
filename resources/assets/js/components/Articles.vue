<template>
  <div>
      <h2>Articles</h2>
      <!-- FORMULARIO de inclusão -->
      <!-- evento submit chama a função de adicionar registro -->
      <form @submit.prevent="addArticle" class="mb-3">
        <div class="form-group">
          <input type="text" class="form-control" placeholder="Title"
          v-model="article.title">
        </div>
        <div class="form-group">
          <textarea type="text" class="form-control" placeholder="Body"
          v-model="article.body"></textarea>
        </div>
        <button type="submit" class="btn btn-light btn-block">Save</button>
      </form>

      <nav aria-label="Page navigation example">
        <!-- fazendo paginação -->
        <ul class="pagination">
          <!-- o botao 'previous' será desabilitado se não houver url para a página anterior -->
          <li v-bind:class="[{disabled: !pagination.prev_page_url}]" class="page-item">
            <!-- @click: indica que um método deve ser chamado quando o elemento for clicado 
                         no caso, será chamado o método fetchArticles() -->
            <a class="page-link" href="#" @click="fetchArticles(pagination.prev_page_url)">Previous</a>
          </li>

          <li class="page-item"><a class="page-link text-dark" 
          href="#">Page {{ pagination.current_page }} of {{ pagination.last_page }}</a></li>
          
          <li v-bind:class="[{disabled: !pagination.next_page_url}]" class="page-item">
            <a class="page-link" href="#" @click="fetchArticles(pagination.next_page_url)">Next</a>
          </li>
        </ul>
      </nav>
      <div class="card card-body mb-2" v-for="article in articles" v-bind:key="article.id">
        <h3>{{ article.title }}</h3>
        <p>{{ article.body }}</p>
        <hr>
        <!-- Botão de exclusão chamando o método ao ser clicado -->
        <button @click="deleteArticle(article.id)" class="btn btn-danger">Delete</button>
      </div>
  </div>
</template>

<script>
  export default {
    data() { // criando o objeto de dados
      return {
        articles: [], // array que receberá os registros
        article: { // objeto com os campos q serao alimentados com dados
          id: '',
          title: '',
          body: ''
        },
        article_id: '', // variável usada como parâmetro no update, pq é usado o mesmo método para inclusão (store)
        pagination: {},
        edit: false // update é false por padrão, pq é usado o mesmo form para inclusão e edição
      }
    },

    // método para buscar os dados automaticamente ao carregar a página
    created() {
      this.fetchArticles(); // o método deve ser definido dentro objeto Methods
    },

    methods: {
      // Método para buscar os dados
      fetchArticles(page_url) { // page_url é usado como parâmetro na paginação
        let vm = this; // atribuindo uma outra variável 'this' para executar o método de paginação que será criado (makepagination)
        page_url = page_url || 'api/articles'
        // usando fetch API
        fetch(page_url)
          .then(res => res.json())
          .then(res => {
            //console.log(res.data);
            // atribuindo os registros no array articles para serem exibidos na tela
            this.articles = res.data;
            // função que efetua a paginação
            // recebe como parâmetros com as urls das páginas usadas na paginação: primeiro, próximo, etc
            vm.makePagination(res.meta, res.links);
          })
          .catch(err => console.log(err));
      },
      // Método de paginação
      makePagination(meta, links) {
        // criando o objeto com os atributos de paginação
        let pagination = {
          current_page: meta.current_page,
          last_page: meta.last_page,
          next_page_url: links.next,
          prev_page_url: links.prev
        }

        this.pagination = pagination; // atribuindo ao valor 'pagination' do objeto 'data'
      },

      deleteArticle(id) {
        // efetuando uma requisição de exclusão na API
        if(confirm('Are you sure?')) {
          fetch(`api/article/${id}`, {
            method: 'delete'
          })
          .then(res => res.json())
          .then(data => {
            alert('Article removed');
            // retorna os artigos
            this.fetchArticles();
          })
          .catch(err => console.log(err));
        }
      },
      
      addArticle() {
        // verifica se o form é de inclusão ou edição
        if (this.edit === false) {
          // Adiciona
          fetch('api/article', {
            method: 'post',
            body: JSON.stringify(this.article),
            headers: {
              'content-type': 'application/json'
            }
          })
          .then(res => res.json())
          .then(data => {
            this.article.title = '';
            this.article.body = '';
            alert('Article added');
            this.fetchArticles();
          })
          .catch(err => console.log(err));
        } else {
          // Atualiza
        }
      }
    }
  }
</script>
