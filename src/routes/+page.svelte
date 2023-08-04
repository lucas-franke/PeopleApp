<script>
	import { onMount } from 'svelte';
	import { writable } from "svelte/store";
	import { Avatar } from '@skeletonlabs/skeleton';
	import { IconMapPin, IconMail, IconPhone } from '@tabler/icons-svelte';
	// Create a writable store for the users array with an initial value of an empty array
	const usersStore = writable([]);
  
	// Function to request random user from api and save as variable wrapped as async function
	const getUsers = async () => {
	  const response = await fetch('https://randomuser.me/api/?results=5');
	  const data = await response.json();
	  const users = data.results.map(user => ({
		name: `${user.name.title} ${user.name.first} ${user.name.last}`,
		location: `${user.location.city}, ${user.location.state}, ${user.location.country}`,
		email: user.email,
		phone: user.phone,
		picture: user.picture.large // Provided image URL
	  }));
	  return users;
	}
  
	// Use a reactive statement to track data loaded state
	let dataLoaded = false;
	onMount(async () => {
	  try {
		const users = await getUsers();
		// Set users array to the store
		usersStore.set(users);
		// Set dataLoaded to true once data is loaded
		dataLoaded = true;
	  } catch (error) {
		console.error("Error fetching user data:", error);
	  }
	});
  </script>

	<div class="space-y-10 text-center flex flex-col items-center p-4">
	  {#each $usersStore as user}
		<!-- Show the Card component for each user with SkeletonUI's .card class -->
		<div class="card bg-gray-100 p-4 w-full sm:max-w-md rounded-lg shadow-lg">
	
		  <div class="card-body flex justify-center items-center mb-4">
			<Avatar src={user.picture} width="w-32" rounded="rounded-full" />
		  </div>
		  <div class="text-xl font-semibold mb-2">{user.name}</div>
		  <div class="text-center">
			<div class="flex flex-col justify-center items-center my-2 py-2	 rounded-l">
				<IconMapPin class="w-32" /><p>{user.location}</p>
			</div>
			<div class="flex flex-col justify-center items-center my-2 py-2	 rounded-l">
				<IconMail class="w-32" /><p>{user.email}</p>
			</div>
			<div class="flex flex-col justify-center items-center my-2 py-2	 rounded-l">
            	<IconPhone class="w-32" /><p>{user.phone}</p>
			</div>
		  </div>
		  <div class="flex justify-end mt-4 gap-2">
			<!-- Add buttons to skip or go back here -->
			<button class="px-4 py-2 bg-gray-300 text-gray-800 rounded">Go Back</button>
			<button class="px-4 py-2 bg-blue-500 text-white rounded">Skip</button>
		  </div>
		</div>
	  {/each}
	  {#if !dataLoaded && $usersStore.length === 0}
		<!-- Show a placeholder when data is loading -->
		<div class="placeholder"></div>
	  {/if}
	  {#if dataLoaded && $usersStore.length === 0}
		<!-- Show a message when there are no users -->
		<p>No users found.</p>
	  {/if}
	</div>
 
  