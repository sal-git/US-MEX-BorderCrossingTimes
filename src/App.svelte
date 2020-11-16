<script>
	import { Tabs, TabList, TabPanel, Tab } from './tabs.js';
	import { onMount } from 'svelte';
	const RSS_URL = 'https://bwt.cbp.gov/api/bwtRss/HTML/44,43,32,23,25,38,35,36,40,39,41,72/42,45,44,43,31,32,23,24,61,25,70,37,38,33,30,35,71,40,39,41,51/42,45,23,24,61,25,37,33,71,40,51';

	let selected;
	let border_rss_data;
	let ped_max_lanes, ped_general_lanes, ped_ready_lanes,
		com_max_lanes, com_general_lanes, com_fast_lanes,
		veh_max_lanes, veh_sentri_lanes, veh_general_lanes, veh_nex_lanes, veh_ready_lanes;

	let border_crossings = [
		{ id: 1, title: 'Brownsville - B&M'},
		{ id: 2, title: 'Brownsville - Gateway'},
		{ id: 3, title: 'Brownsville - Los Indios'},
		{ id: 4, title: 'Brownsville - Veterans International'},
		{ id: 5, title: 'Eagle Pass - Bridge I'},
		{ id: 6, title: 'Eagle Pass - Bridge II'},
		{ id: 7, title: 'El Paso - Bridge of the Americas (BOTA)'},
		{ id: 8, title: 'El Paso - Paso Del Norte (PDN)'},
		{ id: 9, title: 'El Paso - Stanton DCL'},
		{ id: 10, title: 'El Paso - Ysleta'},
		{ id: 11, title: 'Hidalgo/Pharr - Anzalduas International Bridge'},
		{ id: 12, title: 'Hidalgo/Pharr - Hidalgo'},
		{ id: 13, title: 'Hidalgo/Pharr - Pharr'},
		{ id: 14, title: 'Laredo - Bridge I'},
		{ id: 15, title: 'Laredo - Bridge II'},
		{ id: 16, title: 'Laredo - Colombia Solidarity'},
		{ id: 17, title: 'Laredo - World Trade Bridge'},
		{ id: 18, title: 'Progreso - Donna International Bridge'},
		{ id: 19, title: 'Progreso - Progreso International Bridge'},
		{ id: 20, title: 'Rio Grande City'},
		{ id: 21, title: 'Roma'},
		{ id: 22, title: 'San Luis - San Luis I'},
		{ id: 23, title: 'San Luis - San Luis II'},
	]

	const resetLaneInfo = () => {
		ped_max_lanes = "N/A" 
		ped_general_lanes = "N/A"
		ped_ready_lanes = "N/A"
		com_max_lanes = "N/A"
		com_general_lanes = "N/A"
		com_fast_lanes = "N/A"
		veh_max_lanes = "N/A"
		veh_sentri_lanes = "N/A"
		veh_general_lanes = "N/A"
		veh_nex_lanes = "N/A"
		veh_ready_lanes = "N/A"
	}

	const updateCrossingView = () => {
		resetLaneInfo();
		var data = border_rss_data.getElementsByTagName("item")[selected].getElementsByTagName("description")[0];
		var s = new XMLSerializer();
		var array_info = s.serializeToString(data).split('<br/>');
		for(var i = 0; i < array_info.length; i++){
			if (array_info[i].includes("Passenger")) {
				veh_max_lanes = array_info[i].split('h4').pop().replace('Maximum Lanes:', '');
				veh_general_lanes = array_info[i + 1].replace('General Lanes:', '');
				veh_sentri_lanes = array_info[i + 2].replace('Sentri Lanes:', '');
				veh_ready_lanes = array_info[i + 3].replace('Ready Lanes:', '');
			};
			if (array_info[i].includes("Pedestrian")) {
				ped_max_lanes = array_info[i].split('h4').pop().replace('Maximum Lanes:', '');
				ped_general_lanes = array_info[i + 1].replace('General Lanes:', '');
				ped_ready_lanes = array_info[i + 2].replace('Ready Lanes:', '');
			};
			if (array_info[i].includes("Commercial")) {
				com_max_lanes = array_info[i].split('h4').pop().replace('Maximum Lanes:', '');
				com_general_lanes = array_info[i + 1].replace('General Lanes:', '');
				com_fast_lanes = array_info[i + 2].replace('Fast Lanes:', '');
			};
		};
		
	};

	onMount(async () => {
		await fetch(RSS_URL)
			.then(response => response.text())
			.then(str => new window.DOMParser().parseFromString(str, "text/xml"))
			.then(data => border_rss_data = data);
	});

</script>


<div class="card">
	<div class="container">
		<div style="text-align: center">
			<span><img src="images/mexico.png" class="banner-icon"></span>
			<span><img src="images/road-barrier.png" class="banner-icon"></span>
			<span><img src="images/united-states.png" class="banner-icon"></span>
		</div>
		<p style="text-align: center">
			<select bind:value={selected} on:change={updateCrossingView}>
				{#each border_crossings as crossing}
					<option value={crossing.id}>
						{crossing.title}
					</option>
				{/each}
			</select>
		</p>
		<Tabs>
			<TabList>
				<Tab>Commerical <img class="tab-icon" src="images/truck.png"></Tab>
				<Tab>Passenger <img class="tab-icon" src="images/eco-car.png"></Tab>
				<Tab>Pedestrian <img class="tab-icon" src="images/students.png"></Tab>
			</TabList>
		
			<TabPanel>
				<h2>Commerical</h2>
				<p><b>Max Lanes:</b> {com_max_lanes}</p>
				<p><b>General Lanes:</b> {com_general_lanes}</p>
				<p><b>Fast Lanes:</b> {com_fast_lanes}</p>
			</TabPanel>
		
			<TabPanel>
				<h2>Passenger</h2>
				<p><b>Maximum Lanes:</b> {veh_max_lanes}</p>
				<p><b>General Lanes:</b> {veh_general_lanes}</p>
				<p><b>Sentri Lanes:</b> {veh_sentri_lanes}</p>
				<p><b>Fast Lanes:</b> {veh_ready_lanes}</p>
			</TabPanel>
		
			<TabPanel>
				<h2>Pedestrian</h2>
				<p><b>Maximum Lanes:</b> {ped_max_lanes}</p>
				<p><b>General Lanes:</b> {ped_general_lanes}</p>
				<p><b>Ready Lanes:</b> {ped_ready_lanes}</p>
			</TabPanel>
		</Tabs>
	</div>
</div>


<style>
	:global(body){
		background-color: lightseagreen;
		background-image: url("https://images.pexels.com/photos/566862/pexels-photo-566862.jpeg?auto=compress&cs=tinysrgb&dpr=3&h=750&w=1260");
		-webkit-background-size: cover;
		-moz-background-size: cover;
		-o-background-size: cover;
		background-size: cover;
    	}
	.tab-icon {
		height: 20px;
	}
	.banner-icon {
		height: 70px;
	}
	.card {
		box-shadow: 0 4px 8px 0 rgba(0,0,0,0.2);
		transition: 0.3s;
		width: 40%;
		height: 50%;
		border-radius: 5px;
		background-color: #fff;
		position: fixed;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -50%);
	}
	.card:hover {
		box-shadow: 0 8px 16px 0 rgba(0,0,0,0.2);
	}
	.container {
		padding: 2px 16px;
		margin: 20px;
	}
</style>
