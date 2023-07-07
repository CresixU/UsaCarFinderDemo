<template>
<v-app>
	<v-app-bar :elevation="3" style="position: static">
		Navigacja
	</v-app-bar>
	<v-container class="mx-auto mt-0 mt-sm-5">
		<RouterView v-slot="{ Component }">
            <Transition name="animation">
                <component :is="Component" />
            </Transition>
        </RouterView>
	</v-container>
</v-app>
</template>

<style scoped>
.animation-enter-active, .animation-leave-active {
	transition: opacity 0.5s ease;
}
.animation-enter-from, .animation-leave-to {
	opacity: 0;
}

</style>
