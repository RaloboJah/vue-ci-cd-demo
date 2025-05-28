<template>
    <div class="theme">
        <div class="container">
            <label class="switch">
                <input class="togglesw" type="checkbox" checked="">
                <div class="indicator left"></div>
                <div class="indicator right"></div>
                <div class="button"></div>
            </label>
        </div>
    </div>
</template>

<script>
export default {
    name: 'checkTheme',
    data: function(){
        return{
            msg:"Salut Toogler"
        }
    }
}
</script>
<style scoped src="../assets/style.css">
</style>