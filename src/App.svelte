<script>
import { text } from "svelte/internal"

	let idNumber = 1;

  const getTodos = function() {
    const p = fetch( '/read', {
      method:'GET' 
    })
    .then( response => response.json() )
    .then( dreams => {
      //console.log(dreams)
      return dreams 
    })
    
    return p
  }

  const getOtherTodos = function() {
    const p = fetch( '/read2', {
      method:'GET' 
    })
    .then( response => response.json() )
    .then( dreams => {
      //console.log(dreams)
      return dreams 
    })
    
    return p
  }
  
  const addTodo = function( e ) {
	const todo = document.querySelector('input').value
	const numScoops = document.getElementById("scoop").value
	const sprinklesBool = document.getElementById("sprinklesBox").checked
	let sprinkles1 = "no"
	if(sprinklesBool === true){
		sprinkles1 = "yes"
	}
    promise = fetch( '/add', {
      method:'POST',
      body: JSON.stringify({ dream:todo, sprinkles:sprinkles1, scoops:numScoops }),
      headers: { 'Content-Type': 'application/json' }
    })
    .then( response => response.json() )
    promise = getTodos();
  }

  const toggleName = function( e ) {
	const place = e.target.getAttribute('todo')
	const nameFound = document.getElementById(place).value
	console.log(nameFound)
    fetch( '/changeName', {
      method:'POST',
      body: JSON.stringify({ id:e.target.getAttribute('todo'), dream:nameFound }),
      headers: { 'Content-Type': 'application/json' }
	})
  }

  const removeOrder = function( e ) {
    console.log(e.target.getAttribute('todo'))
    fetch( '/delete', {
      method:'POST',
      body: JSON.stringify({ id:e.target.getAttribute('todo') }),
      headers: { 'Content-Type': 'application/json' }
	}).then( response => response.json() )
  promise = getTodos();
  }

  let promise = getTodos()
  let promise2 = getOtherTodos()

</script>

<div class="container">
<h2>Oh hi,</h2>
<p>Tell me your ice cream order:</p>
<form>
	<label> Your Flavor:
		<input type='text' required>
	</label>
	<label>
          Number of Scoops
          <input id="scoop" type="number" maxlength="100" placeholder="Scoops!"required>
    </label>
	<label>
          Check For Sprinkles
          <input id="sprinklesBox" type="checkbox" />
    </label>
</form>
<button on:click={addTodo}>add order</button>

</div>

<p>If you would like to change the flavor of ice cream you ordered, please click in the flavor cell, type your new flavor, and click UPDATE.</p>
<p>If you would like to delete your order, please select DELETE.</p>
  
{#await promise then dreams}
<main>
<table>
	<tr>
		<td>What did you get?</td>
		<td>How Many Scoops?</td>
		<td>Any Sprinkles?</td>
		<td></td>
		<td></td>
	</tr>
  {#each dreams as todo}
	<tr>
		<td><input id={todo._id} type='text' todo={todo.dream} placeholder={todo.dream}></td>
		<td>{todo.scoops}</td>
		<td>{todo.sprinkles}</td>
		<td todo={todo._id} on:click={removeOrder}>DELETE </td>
		<td todo={todo._id} on:click={toggleName}>UPDATE</td>
	</tr>
  {/each}
</table>
</main>
{/await}  

<h2>hey there, here's all our other orders</h2>

{#await promise2 then dreams}
<main>
<table>
	<tr>
		<td>What did you get?</td>
		<td>How Many Scoops?</td>
		<td>Any Sprinkles?</td>
	</tr>
  {#each dreams as todo}
	<tr>
		<td>{todo.dream}</td>
		<td>{todo.scoops}</td>
		<td>{todo.sprinkles}</td>
	</tr>
  {/each}
</table>
</main>
{/await}  