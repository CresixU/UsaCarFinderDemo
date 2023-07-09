<template>
<div>
	<v-row>
		<v-col cols="12">
			<h2><v-skeleton-loader :loading="loading" type="text" class="d-block">{{car.auction_name}}</v-skeleton-loader></h2>
			<p v-if="car.seller">Sprzedawca: <v-skeleton-loader :loading="loading" type="text" class="d-block">{{ car.seller }}</v-skeleton-loader></p>
		</v-col>
		<v-col cols="12" lg="8">
			<v-skeleton-loader :loading="loading" type="image" class="d-block">
				<v-carousel>
					<v-carousel-item
					v-for="(image,i) in car.car_photo.photo"
					:key="i"
					:src="image"
					>
					</v-carousel-item>
				</v-carousel>
			</v-skeleton-loader>
		</v-col>
		<v-col cols="12" lg="4">
			<v-skeleton-loader :loading="loading" type="card" class="d-block">
				<v-expansion-panels>
					<v-expansion-panel title="Historia sprzedaży" >
						<template v-slot:text>
							<h4 v-if="!car.sales_history">Brak historii</h4>
							<div v-for="(item,i) in car.sales_history" :key="i">
								<p>Status aukcji: {{ item.sale_status }}</p>
								<p>Cena zakupu: {{ item.purchase_price }}$</p>
								<p>Data: {{ getFormattedDate(new Date(parseInt(item.saleUtcDateTime))) }}</p>
								<p>Kraj: {{ item.buyer_country }}, Stan: {{ item.buyer_state }}</p>
								<v-divider class="my-2"></v-divider>
							</div>
						</template>
					</v-expansion-panel>
					<v-expansion-panel title="Aktywna licytacja" >
						<template v-slot:text>
							<h4 v-if="!car.active_bidding">Aktualnie nie odbywa się licytacja</h4>
							<div v-for="(item,i) in car.active_bidding" :key="i">
								<p>Data: {{ item.saleUtcDateTime ? getFormattedDate(new Date(parseInt(item.saleUtcDateTime))) : 'Brak' }}</p>
								<p>Aktualna stawka: {{ item.current_bid }}$</p>
								<v-divider class="my-2"></v-divider>
							</div>
						</template>
					</v-expansion-panel>
				</v-expansion-panels>
			</v-skeleton-loader>
		</v-col>
		<v-divider></v-divider>
		<v-col cols="12">
			<v-table>
				<tbody>
					<tr><td>VIN</td> <td><v-skeleton-loader :loading="loading" type="text" class="d-block">{{ car.vin }}</v-skeleton-loader></td></tr>
					<tr><td>Szacowana wartość detaliczna</td> <td><v-skeleton-loader :loading="loading" type="text" class="d-block">{{ car.est_retail_value }}$</v-skeleton-loader></td></tr>
					<tr><td>Marka</td> <td><v-skeleton-loader :loading="loading" type="text" class="d-block">{{ car.make }}</v-skeleton-loader></td></tr>
					<tr><td>Model</td> <td><v-skeleton-loader :loading="loading" type="text" class="d-block">{{ car.model }}</v-skeleton-loader></td></tr>
					<tr><td>Rok</td> <td><v-skeleton-loader :loading="loading" type="text" class="d-block">{{ car.year }}</v-skeleton-loader></td></tr>
					<tr><td>Ilość cylindrów</td> <td><v-skeleton-loader :loading="loading" type="text" class="d-block">{{car.cylinders}}</v-skeleton-loader></td></tr>
					<tr><td>Napęd</td> <td><v-skeleton-loader :loading="loading" type="text" class="d-block">{{car.drive}}</v-skeleton-loader></td></tr>
					<tr><td>Silnik</td> <td><v-skeleton-loader :loading="loading" type="text" class="d-block">{{car.engine_type}}</v-skeleton-loader></td></tr>
					<tr><td>Kluczyki</td> <td><v-skeleton-loader :loading="loading" type="text" class="d-block">{{car.car_keys}}</v-skeleton-loader></td></tr>
					<tr><td>Kolor</td> <td> <v-skeleton-loader :loading="loading" type="text" class="d-block">{{car.color}}</v-skeleton-loader></td></tr>
					<tr><td>Typ nadwozia</td> <td> <v-skeleton-loader :loading="loading" type="text" class="d-block">{{car.body_style}}</v-skeleton-loader> </td> </tr>
					<tr><td>Przebieg</td> <td><v-skeleton-loader :loading="loading" type="text" class="d-block">{{ car.odometer }}</v-skeleton-loader></td></tr>
					<tr><td>doc_type</td> <td><v-skeleton-loader :loading="loading" type="text" class="d-block">{{car.doc_type}}</v-skeleton-loader></td></tr>
					<tr><td>Uszkodzenie główne</td> <td><v-skeleton-loader :loading="loading" type="text" class="d-block">{{ car.primary_damage }}</v-skeleton-loader></td></tr>
					<tr><td>Uszkodzenia drugorzędne</td> <td><v-skeleton-loader :loading="loading" type="text" class="d-block">{{ car.secondary_damage }}</v-skeleton-loader></td></tr>
					<tr><td>Paliwo</td> <td><v-skeleton-loader :loading="loading" type="text" class="d-block">{{ car.fuel }}</v-skeleton-loader></td></tr>
					<tr><td>highlights</td> <td><v-skeleton-loader :loading="loading" type="text" class="d-block">{{ car.highlights }}</v-skeleton-loader></td></tr>
					<tr><td>Lokalizacja</td> <td><v-skeleton-loader :loading="loading" type="text" class="d-block">{{ car.location }}</v-skeleton-loader></td></tr>
					<tr><td>lot_number</td> <td><v-skeleton-loader :loading="loading" type="text" class="d-block">{{ car.lot_number }}</v-skeleton-loader></td></tr>
					<tr><td>Seria</td> <td><v-skeleton-loader :loading="loading" type="text" class="d-block">{{ car.series }}</v-skeleton-loader></td></tr>
					<tr><td>transmission</td> <td><v-skeleton-loader :loading="loading" type="text" class="d-block">{{ car.transmission }}</v-skeleton-loader></td></tr>
					<tr><td>Typ pojazdu</td> <td><v-skeleton-loader :loading="loading" type="text" class="d-block">{{ car.vehicle_type }}</v-skeleton-loader></td></tr>
					
				</tbody>
			</v-table>
		</v-col>
	</v-row>
	<v-row>
		<v-col>
			<v-sheet
			:elevation="2"
			border
			rounded
			class="pa-5">
				<h2 class="mb-5">Kalkulator kosztów</h2>
				<v-form @input="calculatePrice()">
					<v-row>
						<v-col cols="12" lg="6">
							<v-text-field
							label="Cena samochodu $"
							v-model="carPrice">
							</v-text-field>
							<v-select
							label="Stan w USA"
							clearable
							v-model="usaState"
							:items="states"
							:item-title="combinedTitleStates"
							item-value="price"
							></v-select>
							<v-select
							label="Transport"
							clearable
							v-model="transport"
							:items="destinations"
							:item-title="combinedTitleDestination"
							item-value="price"
							></v-select>
							<div>
								<p>Pojazd rekreacyjny podlegający cłom odwetowym</p>
								<v-switch v-model="extraTariff" :label="extraTariff ? 'Tak' : 'Nie'"></v-switch>
							</div>
							<div>
								<p>Pojazd powyżej 30 lat</p>
								<v-switch v-model="carOver30" :label="carOver30 ? 'Tak' : 'Nie'"></v-switch>
							</div>
							<div v-if="!carOver30">
								<div>
									<p>Silnik powyżej 2.0L</p>
									<v-switch v-model="engineOver2l" :label="engineOver2l ? 'Tak' : 'Nie'"></v-switch>
								</div>
							</div>
						</v-col>
						<v-col cols="12" lg="6">
							<v-skeleton-loader :loading="loading" type="paragraph@5" class="d-block">
								<v-table class="calculator">
									<tbody>
										<tr><td>Eksportowa opłata spedycyjna</td><td> 150$</td></tr>
										<tr><td>Opłata aukcyjna</td><td>540$</td></tr>
										<tr v-if="!carOver30"><td>Cło {{ extraTariff ? '35%' : '10%' }}</td><td>{{ obliczClo()*carPrice }}$</td></tr>
										<tr><td>Podatek {{ carOver30 ? '9%' : '21%' }}</td><td>{{ obliczPodatek()*carPrice }}$</td></tr>
										<tr><td>Opłata spedycyjna (rozładunek auta, formalności celne)</td><td>490$</td></tr>
										<tr v-if="!carOver30"><td>Akcyza w zależności od pojemności silnika {{ engineOver2l ? '18.6' : '3.1' }}%</td><td>{{ obliczAkcyza()*carPrice }}$</td></tr>
										<tr><td>Cena w zależności od stanu USA</td><td>{{ usaState ? `${usaState}$` : 'Nie wybrano'}}</td></tr>
										<tr><td>Transport</td><td>{{ transport ? `${transport}$` : 'Nie wybrano' }}</td></tr>
									</tbody>
								</v-table>
							</v-skeleton-loader>
						</v-col>
						<v-divider></v-divider>
						<v-col cols="12">
							<v-skeleton-loader :loading="loading" type="text" class="d-block">
								<div style="text-align: right; font-weight: bold" class="px-5">{{ isNaN(currentPrice) || transport == "0" || usaState == "0" ? 'Nie uzupełniono wszystkich pól' : `Suma: ${currentPrice}$` }}</div>
							</v-skeleton-loader>
						</v-col>
					</v-row>
				</v-form>

			</v-sheet>
		</v-col>
	</v-row>
</div>
</template>

<script>
import jsonFile from '@/assets/exampleapi.json'
import localizations from '@/assets/states.json'
import { VSkeletonLoader } from 'vuetify/labs/VSkeletonLoader';

export default {
	components: {
		VSkeletonLoader
	},
	data() {
		return {
			vin: '',
			carPrice: 0,
			data: null,
			car: null,
			lastSale: null,
			extraTariff: false,
			carOver30: false,
			engineOver2l: false,
			usaState: '0',
			transport: '0',
			states: localizations.states,
			destinations: localizations.destinations,
			currentPrice: 0,
			loading: false
		}
	},
	methods: {
		async GetCarData() {
			this.loading = true
            const url = `https://api.copart-iaai-api.com/example-api.json`
			//const url = `https://api.copart-iaai-api.com/api/v1/get-car-vin?vin_number=5UXTS3C56J0Y98331&api_token=7471ef49828d1441aeb4516ec5bf76cfb62fa52e4125d04bc71b41dcfce0f829`
            const response = await fetch(url, {
                method: 'GET',
                credentials: 'include',
                headers: {
                     'Content-Type': 'application/json',
                }
            })
            this.data = await response.json()
			this.loading = false
        },
		calculatePrice() {
			let clo = this.obliczClo()
			let akcyza = this.obliczAkcyza()
			let podatek = this.obliczPodatek()
			let oplataSpedycyjna = 490
			let eksportowaOplataSpedycyjna = 150
			let opalataAukcyjna = 540
			
			this.currentPrice = parseFloat(this.carPrice) + parseFloat(this.carPrice*clo) + parseFloat(this.carPrice * akcyza) + parseFloat(this.carPrice * podatek) + parseFloat(oplataSpedycyjna) + parseFloat(eksportowaOplataSpedycyjna) + parseFloat(opalataAukcyjna) + parseFloat(this.usaState) + parseFloat(this.transport)
		},
		obliczClo() {
			let clo = 0.1
			if(this.extraTariff) clo = 0.35 
			if(this.carOver30) clo = 0
			return clo
		},
		obliczAkcyza() {	
			let akcyza = 0.031	
			if(this.engineOver2l) akcyza = 0.186
			if(this.carOver30) akcyza = 0
			return akcyza
		},
		obliczPodatek() {
			let podatek = 0.09
			if(!this.carOver30) podatek = 0.21
			return podatek
		},
		combinedTitleStates(value) {
			if (value.name == undefined) return '';
			return `${value.id} - ${value.name}: ${value.price}$`;
		},
		combinedTitleDestination(value) {
			if (value.name == undefined) return '';
			return `${value.name}: ${value.price}$`;
		},
		getFormattedDate(date) {
			if(date == null || date == undefined || date == '') return
			return `${this.AddZeroToNumber(date.getDate())}.${this.AddZeroToNumber(date.getUTCMonth()+1)}.${date.getFullYear()}, godzina: ${this.AddZeroToNumber(date.getHours())}:${this.AddZeroToNumber(date.getMinutes())}`
		},
		AddZeroToNumber(number) {
			if(number >= 0 && number <= 9) return `0${number}`
			return number
			},
		},
	created() {
		this.vin = this.$route.query.vin
		this.data = jsonFile
		this.car = this.data.result[0]
		this.lastSale = this.car.sales_history[this.car.sales_history.length-1]
		this.carPrice = this.car.est_retail_value
	},
	watch: {
		carPrice() {
			this.calculatePrice()
		},
		extraTariff()  {
			this.calculatePrice()
		},
		carOver30()  {
			this.calculatePrice()
		},
		engineOver2l()  {
			this.calculatePrice()
		},
		usaState()  {
			this.calculatePrice()
		},
		transport()  {
			this.calculatePrice()
		}
	},
	computed: {
		calculatedAkcyza(value) {
			return (value) => this.carPrice * this.obliczAkcyza()
		}
	}
}
</script>
<style scoped>
.calculator tr td:nth-child(2) {
	text-align: right;
}
.hide {
	opacity: 0;
}
</style>
