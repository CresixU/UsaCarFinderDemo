<template>
<div>
    <v-text-field label="Wprowadź VIN pojazdu" v-model="vin"> </v-text-field>
	<div class="d-flex justify-center">
        <RouterLink :to="{name: 'car', query: { vin: this.vin }}"> 
            <v-btn 
                class="my-5"
                size="x-large"
                v-ripple
                theme="light"
                rounded="lg">
                Przejdź dalej
            </v-btn>
        </RouterLink>
    </div>
</div>
</template>
<script>
export default {
	data() {
		return {
			vin: '5UXTS3C56J0Y98331',
			destination: `car?vin=${this.vin}`
		}
	}
}
</script>

