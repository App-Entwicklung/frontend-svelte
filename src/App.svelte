<script>
  import { onMount } from 'svelte';
  import contract from './Contract.js';

  let contractValue = null;
  let userAddress = null;
  let newUsername = ''; // Die Variable, die den Wert des Texteingabefelds speichert
  let contactAdressToSendMessage = null;
  let message = null;

	async function connectWallet() {
		if (window.ethereum) {
			// ethereum is an object injected by the wallet. Let's check if is available
			const accounts = await window.ethereum.request({ method: 'eth_requestAccounts' }); // use the request method to get the accounts, aka logging in to Metamask
			if (accounts.length > 0) {
				// it returns an array of accounts, it should have at least 1 element
				userAddress = accounts[0]; // update the state
			} else {
				alert('No ethereum accounts found');
			}
		} else {
			alert('No ethereum Wallet found');
		}
	}


  async function getName() {
    contractValue = await contract.methods.getName().call({
		from: userAddress,
	});
  console.log(contractValue)
  }

  onMount(async () => {
    await getName();
  });


  
	async function setName() {
		await contract.methods.setName(newUsername).send({
		from: userAddress
	});
  }

  onMount(async () => {
    await setName();
  });

  async function createAccount() {
		await contract.methods.createAccount(newUsername).send({
		from: userAddress
	});
  }

  onMount(async () => {
    await createAccount();
  });

  async function sendMessage() {
		await contract.methods.sendMessage(contactAdressToSendMessage, message).send({
		from: userAddress
	});
  }

  onMount(async () => {
    await sendMessage();
  });

  function handleSubmitNewUsername() {
    console.log(newUsername); // Gibt den Wert der Variable `name` in der Konsole aus
	console.log(contactAdressToSendMessage);
	console.log(message);
  }



</script>

<main>

<h1>Account Adress: {userAddress}</h1>
<button on:click={connectWallet}>Connect</button>

<h1>Your Username is: {contractValue}</h1>
<button on:click={getName}>Read Name</button>


<form on:submit|preventDefault={handleSubmitNewUsername}>
	<label for="name">Name:</label>
	<input type="text" id="name" bind:value={newUsername}>
	<button on:click={setName}>Change Your Name</button>
	<button on:click={createAccount}>Create Your New Account</button>
	<label for="contactAdressToSendMessage">Adress to send message:</label>
	<input type="text" id="contactAdressToSendMessage" bind:value={contactAdressToSendMessage}>
	<label for="message">Message:</label>
	<input type="text" id="message" bind:value={message}>
	<button on:click={sendMessage}>Send Message</button>
  </form>

</main>
