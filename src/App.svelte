<script>
	import { setContext,onMount,afterUpdate } from 'svelte';
	// components
	import Navbar from "./components/Navbar.svelte";
	import ExpensesList from "./components/ExpensesList.svelte";
	import Total from "./components/Total.svelte";
	import ExpenseForm from "./components/ExpenseForm.svelte";
	import Modal from "./components/Modal.svelte";
	//import Github from "./components/Github.svelte";
	import GithubAwait from "./components/GithubAwait.svelte";
	// data
	//import expensesData from "./expenses";
	// variables
	let expenses = [];
	// set editing variable
	 let setId = null;
	 let setName = "";
	 let setAmount = null;
	 // toggle form variable
	 let isFormOpen = false;
	// reactive
	$: total = expenses.reduce((acc,curr) => {
		return acc += curr.amount;
	},0);
	$: isEditing = setId ? true:false;



	// functions
	function removeExpense(id) {
		expenses = expenses.filter(item => item.id !== id);
		
	}
	function clearExpenses(){
		expenses = [];
		
	}
	function addExpense({name,amount}){
		let expense = {id:Math.random()*Date.now(),name,amount};
		expenses = [expense,...expenses];
	
	}
	function setModifiedExpense(id){
		let expense = expenses.find(item => item.id === id);
		setId = expense.id;
		setName = expense.name;
		setAmount = expense.amount;
		showForm();
	}
	function editExpense({name,amount}){
		expenses = expenses.map(item => {
			return item.id === setId ? {...item,name,amount}:{...item};
		});
		setId = null;
		setName = "";
		setAmount = null;
		
	}
	function showForm(){
		isFormOpen = true;
	}
	function hideForm(){
		isFormOpen = false;
		setId = null;
		setName = "";
		setAmount = null;
	}
	// local storage
	function setLocalStorage(){
		localStorage.setItem('expenses',JSON.stringify(expenses));
	}
	onMount(() => {
		expenses = localStorage.getItem('expenses') ? JSON.parse(localStorage.getItem('expenses')) : [];
	});

	afterUpdate(() => {
		setLocalStorage();
	});
	// context
	setContext('remove',removeExpense);
	setContext('modify',setModifiedExpense);
</script>

<Navbar {showForm}/>
<main class="content">	
	{#if isFormOpen}	
		<Modal>
			<ExpenseForm {addExpense} name={setName} amount={setAmount} {isEditing} {editExpense} {hideForm}/>
		</Modal>
	{/if}
	<Total title="total expense" {total}/>
	<ExpensesList {expenses} />
	<button type="button" class="btn btn-primary btn-block" on:click={clearExpenses}>clear expenses</button>
		<!-- <GithubAwait /> -->
</main>
