<template>
    <div id="app">
        <MainPage/>
    </div>
</template>

<script>
    import MainPage from './components/MainPage.vue'

    export default {
        name: 'App',
        components: {
            MainPage
        }
    }
</script>

<style lang="scss">
	body {
		margin: 0;
	}

	#app {
        background-color: #f7f7f7;
	}
</style>