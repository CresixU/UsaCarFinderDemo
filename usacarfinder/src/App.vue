<template>
<v-app>
	<v-app-bar :elevation="3">
		<v-container>
			<RouterLink to="/" style="font-size: 22px">
				<p><v-icon style="margin-top: -6px" icon="mdi-home"/> Strona główna</p>
			</RouterLink>
		</v-container>
	</v-app-bar>
	<v-container class="mx-auto" style="margin-top: 100px">
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
a {
	text-decoration: none;
}
a:visited {
	color: black;
}
</style>
