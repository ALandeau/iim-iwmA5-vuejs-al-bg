<template>
	<div>
		<a class="navbar-item" v-on:click="modalToggle(true)">{{ create | capitalize }}</a>
		<div class="modal" v-bind:class="{'is-active': isActive}">
			<div class="modal-background" v-on:click="modalToggle(false)"></div>
			<div class="modal-content">
				<section class="modal-card-body">
					<form>
						<div class="field">
							<div class="control has-icons-left has-icons-right">
								<input class="input is-medium" type="text" placeholder="Nom" v-model="name">
							</div>
						</div>

						<div class="field">
							<div class="control is-expanded">
								<div class="select is-fullwidth">
								<select name="country" v-model="departure">
									<option v-for="destination in destinations" v-bind:key="destination.id" :value="destination.id">{{ destination.name }}</option>
								</select>
								</div>
							</div>
						</div>

						<div class="field">
							<div class="control is-expanded">
								<div class="select is-fullwidth">
								<select name="country" v-model="arrived">
									<option v-for="destination in destinations" v-bind:key="destination.id" :value="destination.id">{{ destination.name }}</option>
								</select>
								</div>
							</div>
						</div>
					</form>
					<a class="navbar-item" v-if="create == 'update'" v-on:click="$emit('updateData', id, {name, departure, arrived}), modalToggle(false)">Save</a>
					<a class="navbar-item" v-if="create == 'create'" v-on:click="$emit('newData', {name, departure, arrived}), modalToggle(false)">Save</a>
    			</section>
			</div>
			<button class="modal-close is-large" v-on:click="modalToggle(false)" aria-label="close"></button>
		</div>
	</div>
</template>

<script>
export default {
	props: [
		'destinations', 
		'create',
		'id',
		'nameP',
		'departureP',
		'arrivedP'
	],
  	data() {
    	return {
			isActive: false,
			name: null,
			departure: null,
			arrived: null
    	}
	},
	created() {
		this.name = this.nameP
		this.departure = this.departureP
		this.arrived = this.arrivedP
	},
  	methods: {
		modalToggle: function(x) {
			this.isActive = x 
		}
	},
	filters: {
		capitalize: function(data) {
			if (!data) return ''
    		data = data.toString()
			return data.charAt(0).toUpperCase() + data.slice(1)
		}
	}
}

</script>
