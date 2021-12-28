<template>
    <svg aria-hidden="true" :width="size" :height="size" focusable="false" data-prefix="fas" data-icon="border-all" role="img" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 448 512" class="svg-inline--fa fa-border-all fa-w-14 fa-3x">
        <path :fill="fill" d="M416 32H32A32 32 0 0 0 0 64v384a32 32 0 0 0 32 32h384a32 32 0 0 0 32-32V64a32 32 0 0 0-32-32zm-32 64v128H256V96zm-192 0v128H64V96zM64 416V288h128v128zm192 0V288h128v128z" class=""/>
    </svg>

</template>

<script>
    export default{
        props: {
            fill: {
                default: function () {
                    return "#000000"
                }
            },
            size: {
                default: function () {
                    return 24
                }
            }
        }
    }
</script>