<script setup lang="ts">
	import { reactive, ref, computed } from 'vue'

	const nameFilter = ref('')
	const statusFilter = ref(false)

	enum Status {
		Open = 'Offen',
		Accepted = 'Angenommen',
		Declined = 'Abgelehnt',
	}

	const applicants = reactive([
		{name: 'Patrick', status: Status.Accepted },
		{name: 'Andreas', status: Status.Declined },
		{name: 'Henning', status: Status.Open },
	])

	const filteredApplicants = computed(() =>
		applicants.filter(applicant => 
			applicant.name.toLocaleLowerCase().includes(nameFilter.value.toLocaleLowerCase()) &&
			(!statusFilter.value || applicant.status != Status.Declined)
		)
	)

	const addApplicant = (event: Event) => {
		const form = event.target as HTMLFormElement
		const name = (form.elements as HTMLFormControlsCollection & {
			name: HTMLInputElement
		}).name.value

		if (!name.length) return
		
		applicants.push({name, status: Status.Open})

		form.reset()
	}
</script>

<template>
	<!-- Add Applicant -->
	<form @submit.prevent="addApplicant">
		<input type="text" name="name">
		<button>Hinzufügen</button>
	</form>

	<hr />

	<!-- Filter -->
	<search>
		<input placeholder="Namensfilter" v-model="nameFilter"/>
		<label>
			<input type="checkbox" v-model="statusFilter" />
			<span>Abgelehnte Bewerber ausblenden</span>
		</label>
	</search>

	<!-- Listing -->
	<table>
		<tr>
			<th>Name</th>
			<th>Status</th>
			<th>Aktion</th>
		</tr>

		<tr v-for="(applicant) in filteredApplicants">
			<td>{{ applicant.name }}</td>
			<td>{{ applicant.status }}</td>
			<td>
				<section v-if="applicant.status === Status.Open">
					<button @click="applicant.status = Status.Accepted">Annehmen</button>
					<button @click="applicant.status = Status.Declined">Ablehnen</button>
				</section>
			</td>
		</tr>
	</table>

</template>

<style scoped>
	/* https://stackoverflow.com/a/58977845 */
	>>> {
		--border-color: black;
		--border-width: 2px;
	}

	form, hr, search, table {
		margin: 0.5rem 0;
	}

	form, search, search label, section {
		display: flex;
		gap: 1rem;
	}

	button {
		cursor: pointer;
		padding: 0px 16px;
		border-radius: 2px;
		height: 32px;
		background-color: rgb(255, 255, 255);
		border: 1px solid rgb(138, 136, 134);
	}

	span {
		white-space: nowrap;
	}

	th, td {
		padding: 0.5rem;
	}

	th {
		text-align: start;
	}

	tr:nth-child(even) {
		background-color: whitesmoke;
	}

	th {
		background-color: gainsboro;
	}

	table td + td { border-left: var(--border-width) solid var(--border-color); }
	table th + th { border-left: var(--border-width) solid var(--border-color); }
	table { border: var(--border-width) solid var(--border-color); border-spacing: 0;}
</style>
