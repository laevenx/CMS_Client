<template>
    <div>
        <b-sidebar
                position="static"
                :expand-on-hover="expandOnHover"
                type="is-light"
                open
            >
                <div class="p-1">
                    <b-menu class="is-custom-mobile">
                        <b-menu-list label="Menu"  v-if="loggedIn">
                            <b-menu-item icon="information-outline" class="has-text-left" label="Account Info" @click="showAccInfo"></b-menu-item>
                            <b-menu-item icon="package"  class="has-text-left " label="Product Menu">
                                <b-menu-item icon="package-variant-closed" label="Product List" @click.prevent="$router.push('/menu')"></b-menu-item>
                                <b-menu-item icon="package-down" label="Add New Product" @click.prevent="showAddProduct"></b-menu-item>
                            </b-menu-item>
                            <b-menu-item icon="filter"  class="has-text-left " label="Category Menu">
                              <b-menu-item icon="cellphone-link" label="Categories List" @click.prevent="showAddCategories"></b-menu-item>
                                <b-menu-item icon="filter-plus" label="Add Categories" @click.prevent="showCategoryList"></b-menu-item>
                            </b-menu-item>
                            <b-menu-item icon="cart-outline"  class="has-text-left " label="Transactions Menu">
                              <b-menu-item icon="cart" label="Transactions List" @click.prevent="showCartList"></b-menu-item>
                                <b-menu-item icon="cart-plus" label="Add Transaction" @click.prevent="showaddCart"></b-menu-item>
                            </b-menu-item>
                            <b-menu-item icon="monitor-dashboard"  class="has-text-left " label="Banner Menu">
                              <b-menu-item  icon="clipboard-multiple-outline" label="Banner List" @click.prevent="showBannerList"></b-menu-item>
                                <b-menu-item  icon="clipboard-plus-outline" label="Add Banner" @click.prevent="showaddBanner"></b-menu-item>
                            </b-menu-item>
                        </b-menu-list>
                        <b-menu-list label="Menu" v-if="!loggedIn">
                            <b-menu-item label="Login" class="has-text-left" icon="account" @click="$router.push('/')"></b-menu-item>
                            <b-menu-item label="Register" class="has-text-left" icon="account" @click="$router.push('/register')"></b-menu-item>
                            <b-menu-item label="About" class="has-text-left" icon="account" @click="$router.push('/about')"></b-menu-item>
                        </b-menu-list>
                        <b-menu-list label="Actions" v-if="loggedIn">
                            <b-menu-item icon="logout" label="Logout" @click.prevent="logout()"></b-menu-item>
                        </b-menu-list>
                    </b-menu>
                </div>
            </b-sidebar>
    </div>
</template>

<script>
// import { mapGetters } from 'vuex'

export default {
  name: 'Menu',
  data () {
    return {
      reduce: false
    }
  },
  watch: {
    test () {
      // console.log(test)
      this.loggedin = this.$store.state.loggedIn
    }
  },
  computed: {
    loggedIn () {
      return this.$store.getters.loggedIn
    }
    // loggedIn () {
    //   return this.$store.state.loggedIn
    // }
  },
  methods: {
    logout () {
      localStorage.clear()
      this.$store.state.loggedIn = false
      this.$store.commit('SET_LOGIN', false)
      this.$router.push('/')
      this.$buefy.toast.open('logged out')
    },
    showAccInfo () {
      this.$router.push('/info')
    },
    showAddProduct () {
      this.$router.push('/addproduct')
    },
    showCategoryList () {
      this.$router.push('/addcategory')
    },
    showAddCategories () {
      this.$router.push('/category')
    },
    showaddCart () {
      this.$router.push('/addcart')
    },
    showCartList () {
      this.$router.push('/cartlist')
    },
    showBannerList () {
      this.$router.push('/bannerlist')
    },
    showaddBanner () {
      this.$router.push('/addbanner')
    }
  },
  created () {
    if (localStorage.token) {
      this.$store.state.loggedIn = true
      this.$store.dispatch('fetchProducts')
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
@import "~bulma/sass/utilities/_all";
$menu-item-active-background-color : hsl(0, 0%, 71%);
$sidebar-background : #000000;
</style>
