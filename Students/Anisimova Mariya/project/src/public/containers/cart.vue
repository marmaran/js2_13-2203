<template>
  <div class="cart-block" v-if="$parent.show">
    <div class="d-flex">
        <strong class="d-block">Всего товаров</strong> <div id="quantity"></div>
    </div>
    <hr>
    <div class="cart-items">
        <item 
        v-for="item of items" 
        :key="item.id_product" 
        :item="item" 
        :type="'cart'"
        @deleteitem="removeItem"/>
    </div>
    <hr>
    <div class="d-flex">
        <strong class="d-block">Общая ст-ть:</strong> <div id="price"></div>
    </div>
</div>
</template>

<script>
import item from '../components/list_item.vue';
export default {
    components: { item },
    data() {
        return {
            items: [],
            url: 'api/cart',
        }
    },
    methods: {
        addItem(item) {
            let find = this.items.find(el => el.id_product == item.id_product);
            if (find) {
                this.$parent.put('/api/cart/' + item.id_product, { amount: 1 })
                    .then(res => {
                        if (res.status) {
                            find.quantity++
                        }
                    })
            } else {
                let newItem = Object.assign({}, item, {quantity: 1})
                this.$parent.post('/api/cart', newItem)
                    .then(res => {
                        if (res.status) {
                            newItem.id_product = item.id_product
                            this.items.push(newItem)
                        }
                    })
                
            }
        },
        removeItem(item) {
            let find = this.items.find(el => el.id_product == item.id_product);
            if (find.quantity > 1) {
                this.$parent.put('/api/cart/' + item.id_product, { amount: -1 })
                    .then(res => {
                        if (res.status) {
                            find.quantity--
                        }
                    })
            } else {
                this.$parent.delete('/api/cart/' + item.id_product)
                    .then(res => {
                        if (res.status) {
                            this.items.splice(this.items.indexOf(find), 1)
                        }
                    })
            }
        }
    },
    mounted() {
        this.$parent.get(this.url)
        .then(data => {this.items = data.contents});
    }
}
</script>

<style>
</style>