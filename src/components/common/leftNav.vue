<template>
    <div class="left-nav">
        <ul class="menu">
            <li class="menu-item" v-for="i in icon" :key='i.name'>
                <svg class="icon" aria-hidden='true'>
                    <use :xlink:href="`#icon-`+i.icon"></use>
                </svg>
                <p class="item-name">{{i.name}}</p>
            </li>
        </ul>
    </div>
</template>


<script>
export default {
    name: 'leftNav',
    data(){
        return {
            icon: [
                {icon: 'cup',name:'Checkout'},
                {icon: 'home',name:'Store'},
                {icon: 'computer',name:'Item'},
                {icon: 'user',name:'Member'},
                {icon: 'xiangce',name:'Statistics'},
                {icon: 'computer',name:'Setting'},
            ]
        }
    }
}
</script>


<style lang="scss" scoped>
    .left-nav {
        background: #4A4A4A;
        float: left;
        width: 5%;
        height: 100vh;

        .menu {
            color: #f4f4f4;

            .menu-item {
                list-style: none;
                padding: 1.5em .5em;
                cursor: pointer;
                &:hover {
                    background: #3a3a3a;
                }

                svg.icon {
                    font-size: 1.5em;
                }

                .item-name {
                    margin-top: 4px;
                    font-size: 0.9em;
                    font-weight: lighter;
                    color: #f6f6f6;

                    @media (max-width: 1200px) {
                        display: none;
                    }
                }
            }
        }

    }
</style>
