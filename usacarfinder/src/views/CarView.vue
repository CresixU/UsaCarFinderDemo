<template>
<div>
	<v-row>
		<v-col cols="8">
			<v-carousel theme="dark">
				<v-carousel-item
				v-for="(image,i) in car.car_photo.photo"
				:key="i"
				:src="image"
				>
				</v-carousel-item>
			</v-carousel>
		</v-col>
		<v-col cols="4">
			<p>Status aukcji: {{ lastSale.sale_status }}</p>
			<p>Cena zakupu: {{ lastSale.purchase_price }}</p>
			<p>Data: {{ (new Date(this.lastSale.saleUtcDateTime).toString()) }}</p>
		</v-col>
		<v-divider></v-divider>
		<v-col cols="12">
			<v-table>
				<tbody>
					<tr><td>Nazwa aukcji</td><td>{{car.auction_name}}</td></tr>
					<tr><td>Kluczyki</td> <td>{{ car.car_keys }}</td></tr>
					<tr><td>Kolor</td> <td> {{ car.color }}</td></tr>
					<tr><td>Typ nadwozia</td> <td> {{ car.body_style }} </td> </tr>
					<tr><td>Ilość cylindrów</td> <td>{{car.cylinders}}</td></tr>
					<tr><td>doc_type</td> <td>{{ car.doc_type }}</td></tr>
					<tr><td>Napęd</td> <td>{{ car.drive }}</td></tr>
					<tr><td>Silnik</td> <td>{{ car.engine_type }}</td></tr>
					<tr><td>szacowana wartość detaliczna</td> <td>{{ car.est_retail_value }}</td></tr>
					<tr><td>Paliwo</td> <td>{{car.fuel}}</td></tr>
					<tr><td>highlights</td> <td>{{ car.highlights }}</td></tr>
					<tr><td>Lokalizacja</td> <td>{{ car.location }}</td></tr>
					<tr><td>lot_number</td> <td>{{car.lot_number}}</td></tr>
					<tr><td>Marka</td> <td>{{ car.make }}</td></tr>
					<tr><td>Model</td> <td>{{ car.model }}</td></tr>
					<tr><td>Przebieg</td> <td>{{car.odometer}}</td></tr>
					<tr><td>Uszkodzenie główne</td> <td>{{car.primary_damage}}</td></tr>
					<tr><td>Uszkodzenia drugorzędne</td> <td>{{car.secondary_damage}}</td></tr>
					<tr><td>Sprzedawca</td> <td>{{ car.seller}}</td></tr>
					<tr><td>Seria</td> <td>{{car.series}}</td></tr>
					<tr><td>transmission</td> <td>{{ car.transmission }}</td></tr>
					<tr><td>Typ pojazdu</td> <td>{{ car.vehicle_type }}</td></tr>
					<tr><td>VIN</td> <td>{{ car.vin }}</td></tr>
					<tr><td>Rok</td> <td>{{ car.year }}</td></tr>
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
				<v-form>
					<v-row>
						<v-col cols="6">
							<v-text-field
							label="Cena samochodu $"
							v-model="carPrice">
							</v-text-field>
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
						<v-col cols="6">
							<v-select
							label="Stan w USA"
							clearable
							v-model="usaState"
							:items="states"
							item-title="name"
							item-value="price"
							></v-select>
							<v-select
							label="Transport"
							clearable
							v-model="transport"
							:items="destinations"
							item-title="name"
							item-value="price"
							></v-select>
							<v-table>
								<tr><td>Eksportowa opłata spedycyjna</td><td> 150$</td></tr>
								<tr><td>Opłata aukcyjna</td><td>540$</td></tr>
								<tr v-if="!carOver30"><td>Cło {{ extraTariff ? '35%' : '10%' }}</td><td></td></tr>
								<tr><td>Podatek {{ carOver30 ? '9%' : '21%' }}</td><td></td></tr>
								<tr><td>Opłata spedycyjna (rozładunek auta, formalności celne)</td><td>490$</td></tr>
								<tr v-if="!carOver30"><td>Akcyza w zależności od pojemności silnika {{ engineOver2l ? '18.6' : '3.1' }}%</td><td></td></tr>
							</v-table>
						</v-col>
						<v-divider></v-divider>
						<v-col cols="6">
							Suma: {{ currentPrice }}$
						</v-col>
						<v-col cols="6">
							<v-btn @click="calculatePrice()">Podlicz</v-btn>
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

export default {
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
			usaState: '',
			transport: '',
			states: localizations.states,
			destinations: localizations.destinations,
			currentPrice: 0
		}
	},
	methods: {
		async GetCarData() {
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
        },
		calculatePrice() {
			let clo = 0.1
			let akcyza = 0.031
			let podatek = 0.21
			let oplataSpedycyjna = 490
			let eksportowaOplataSpedycyjna = 150
			let opalataAukcyjna = 540
			if(this.extraTariff) clo = 0.35 
			if(this.carOver2l) akcyza = 0.186
			if(this.carOver30) {
				clo = 0
				akcyza = 0
			}
			this.currentPrice = parseFloat(this.carPrice) + parseFloat(this.carPrice*clo) + parseFloat(this.carPrice * akcyza) + parseFloat(this.carPrice * podatek) + parseFloat(oplataSpedycyjna) + parseFloat(eksportowaOplataSpedycyjna) + parseFloat(opalataAukcyjna) + parseFloat(this.usaState) + parseFloat(this.transport)
		}
	},
	created() {
		this.vin = this.$route.query.vin
		this.data = jsonFile
		this.car = this.data.result[0]
		this.lastSale = this.car.sales_history[this.car.sales_history.length-1]
	}
}
</script>
