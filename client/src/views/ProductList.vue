
<template>
    <section>

        <b-table
            :data="isEmpty ? [] : products"
            :bordered="isBordered"
            :striped="isStriped"
            :narrowed="isNarrowed"
            :hoverable="isHoverable"
            :loading="isLoading"
            :focusable="isFocusable"
            :mobile-cards="hasMobileCards">

            <template slot-scope="temp">
                <b-table-column field="id" label="ID" width="40" numeric>
                    {{ temp.row.id }}
                </b-table-column>

                <b-table-column field="name" label="Name" :searchable="true">
                    {{ temp.row.name }}
                </b-table-column>

                <b-table-column field="category" label="Category" :searchable="false">
                    {{ temp.row.Category.name }}
                </b-table-column>

                <b-table-column field="image_url" label="Image">
                  <img v-bind:src="temp.row.image_url" height="64" width="64">
                </b-table-column>

                <b-table-column field="stock" label="Stock">
                    {{ temp.row.stock }}
                </b-table-column>

                <b-table-column field="price" label="Price">
                    {{ temp.row.price }}
                </b-table-column>

                <b-table-column label="Action">
                    <span>
                      <div class="buttons">
                         <b-button @click="showEditMenu(temp.row.id)" type="is-primary">Edit</b-button>
                          <b-button @click="deleteProduct(temp.row.id)" type="is-primary">Delete</b-button>
                          </div>
                    </span>
                </b-table-column>
            </template>

            <template slot="empty">
                <section class="section">
                    <div class="content has-text-grey has-text-centered">
                        <p>
                            <b-icon
                                icon="emoticon-sad"
                                size="is-large">
                            </b-icon>
                        </p>
                        <p>Nothing here.</p>
                    </div>
                </section>
            </template>
        </b-table>
        <b-modal :active.sync="isComponentModalActive"
            has-modal-card
            full-screen
            aria-role="dialog"
            aria-modal
            trap-focus
            :can-cancel="false">
             <form action="">
                <div class="modal-card" style="position:absolute;bottom:25%;left:25%;width: 50%;">
                    <header class="modal-card-head">
                        <p class="modal-card-title">Edit Product</p>
                    </header>
                    <section class="modal-card-body">
                        <b-field label="name">
                            <b-input
                                v-model="name"
                                type="text"
                                :value="name"
                                placeholder="Product Name"
                                required>
                            </b-input>
                        </b-field>

                        <b-field label="Image URL">
                            <b-input
                                v-model="image_url"
                                type="url"
                                :value="image_url"
                                placeholder="Image URL"
                                required>
                            </b-input>
                        </b-field>

                        <b-field label="Stock">
                            <b-input
                                v-model="stock"
                                type="text"
                                :value="stock"
                                placeholder="Stock"
                                required>
                            </b-input>
                        </b-field>

                        <b-field label="Price">
                            <b-input
                                v-model="price"
                                type="text"
                                :value="price"
                                placeholder="Price"
                                required>
                            </b-input>
                        </b-field>

                    </section>
                    <footer class="modal-card-foot">
                        <button class="button" type="button" @click="close">Close</button>
                        <button class="button is-primary" @click.prevent="edit(selectId)">Edit Product</button>
                    </footer>
                </div>
            </form>
        </b-modal>
    </section>
</template>

<script>
import server from '@/api'
// import ProductCard from '@/components/ProductCard'
export default {

  name: 'ProductList',
  data () {
    return {
      isEmpty: false,
      isBordered: false,
      isStriped: false,
      isNarrowed: false,
      isHoverable: false,
      isFocusable: false,
      isLoading: false,
      hasMobileCards: true,
      isComponentModalActive: false,
      selectId: 0,
      CategoryId: 0
    }
  },
  methods: {
    showEditMenu (id) {
      server.get(`/products/${id}`, {
        headers: {
          token: localStorage.token
        }
      })
        .then(({ data }) => {
          this.isComponentModalActive = true
          this.selectId = data.id
          this.name = data.name
          this.image_url = data.image_url
          this.stock = data.stock
          this.price = data.price
          this.CategoryId = data.CategoryId
        })
        .catch(err => {
          console.log(err.response)
        })
    },
    edit (id) {
      console.log(id)
      console.log(this.selectId)
      const updatedData = {
        name: this.name,
        image_url: this.image_url,
        stock: this.stock,
        price: this.price,
        CategoryId: this.CategoryId
      }
      console.log(updatedData)
      server.put(`/products/edit/${id}`, updatedData, {
        headers: {
          token: localStorage.token
        },
        params: {
          id: this.selectId
        }
      })
        .then(({ data }) => {
          this.$store.commit('SET_UPDATEPRODUCT', data)
          this.$store.dispatch('fetchProducts')
          console.log('edit product completed')
          this.isComponentModalActive = false
          this.$buefy.toast.open('Edit Product Completed')
          // this.$store.dispatch('fetchProducts')
          //   .finally(_ => {
          //     this.data = this.$store.state.products
          //   })
        })
        .catch(err => {
          console.log(err.response.data)
          this.$buefy.snackbar.open({
            duration: 5000,
            message: err.response.data.error[0],
            type: 'is-danger',
            position: 'is-top',
            queue: true

          })
        })
    },
    deleteProduct (id) {
      server.delete(`/products/delete/${id}`, {
        headers: {
          token: localStorage.token
        }
      })
        .then(({ data }) => {
          this.$store.commit('SET_DELETEPRODUCT', { info: 'deleted' })
          this.$store.dispatch('fetchProducts')
          this.$buefy.toast.open('Delete Product Completed')
        })
        .catch(err => {
          console.log(err.response)
        })
    },
    close () {
      this.isComponentModalActive = false
    }
  },
  created () {
    this.$store.dispatch('fetchProducts')
    this.$store.dispatch('fetchCategory')
    // .finally(_ => {
    //   console.log('asd', this.$store.state.products)
    //   this.fetchProduct()
    // })
  },
  computed: {
    products () {
      return this.$store.getters.products
    },
    categories () {
      return this.$store.getters.categories
    }
  }
}

</script>

<style>

</style>
