<script>



  // init params
  let userObject = null;
 
  const userbase = window.userbase;



  let authPromise = userbase.init({appId: 'd6cdd115-9b95-4219-a91b-452ef2d3757d'}).then(({user}) => userObject = user);


  // auth params
  let username, password;

  const signIn = () => authPromise = userbase.signIn({username, password}).then(user => userObject = user); 
  const signUp = () => authPromise = userbase.signUp({username, password}).then(user => userObject = user);
  const signOut = () => authPromise = userbase.signOut().then(userObject = null);

  $: console.log(userObject);

  // navigation params

  function nextStudent() {
    if(index === students.length -1){
      index = 0;
    }
    index++;
  }

  function previousStudent(){
    if(index === 0) {
      index = students.length;
    }
    index--;
  }

  //database params

  let updatedStudent = {
    name: '',
    studentNumber: 0,
    birthday: ''
  };

  let index = 0;
  let studentPromise;
  let students = [], newStudent = {
    name: '',
    studentNumber: 0,
    birthday: ''
  };
  const databaseName = 'students';
  function changeHandler(items) {students = items} 
  $: if (userObject) studentPromise = userbase.openDatabase({databaseName, changeHandler})

  function addStudent() {
    studentPromise = userbase.insertItem({databaseName, item: newStudent});
    newStudent = {
    name: '',
    studentNumber: 0,
    birthday: new Date(2020, 11, 25)
  };
  }

  let showAll = false;

  function deleteStudent(itemId) {
    studentPromise = userbase.deleteItem({databaseName, itemId});
  }

  function updateStudent(index) {
    const {item, itemId} = students[index];
    studentPromise = userbase.updateItem({databaseName, itemId, item});
  }

  function toggleAll(){
    showAll = !showAll;
  }


  
  </script>

  



<main>
  <h1>Algoritmos e Programação 2</h1>
  <p>
    Exemplo de padrão <a href="https://pt.wikipedia.org/wiki/MVC">MVC</a>
    utilizando o framework <a href="https://svelte.dev/">SvelteJS</a> (com
    Typescript), banco de dados <a href="https://userbase.com/">Userbase</a>
  </p>
  <p>
    A implementação busca se assemelhar a que foi empregada nos exemplos do
    professor Akira em funcionalidade e visual. <br> Porém os padrões de projetos (como o Repository Pattern) não foram utilizados.
  </p>
  <p>Apesar de que o Userbase seja relativamente seguro, nenhum esforço foi feito quando a segurança desse aplicativo</p>
  <p style="color: red"> Não utilize dados pessoais cruciais  </p>

{#await authPromise }
  {console.log(authPromise)}
  Carregando :D ...
{:then _} 
{#if !userObject}
<form class="login" >
  <p style="margin-bottom: 50px;">Para fazer alterações, você precisa logar!</p>
  <label for="username">
    Usuário
  </label>
  <input id="username" bind:value={ username } type="text">

  <label for="password">
    Senha
  </label>
  <input id="password" bind:value={ password } type="password">

  <div>
    <button on:click={ signIn } type="button" >Entrar</button>
  <button on:click={ signUp } type="button" >Registrar!</button>
  </div>
</form>
{:else}
    <h1>Olá, {userObject.username}!</h1>
    
  
    
  
    <form>
    
      <label for="name"> Nome </label>
      <input bind:value={ newStudent.name } type="text" />
  
      <label for="registration"> Matrícula </label>
      <input bind:value={ newStudent.studentNumber } type="number" />
  
      <label for="date"> Data de Nascimento </label>
      <input bind:value={ newStudent.birthday } type="date" />
    </form>
  
    <div class="buttons">
   
      <button on:click={previousStudent} class="action-button">
        <img class="icon-button" src="assets/images/previous.png" alt="">
      </button>
  
      <button on:click={ () => updateStudent(index)} class="action-button">
        <img class="icon-button" src="assets/images/refresh.png" alt="">
      </button>

      <button on:click={ () => deleteStudent(students[index].itemId) } class="action-button">
        <img  class="icon-button" src="assets/images/deletar-usuario.png" alt="">
      </button>
  
      <button on:click={addStudent} class="action-button">
        <img class="icon-button" src="assets/images/diskette.png" alt="">
      </button>
  
      <button on:click={toggleAll} class="action-button">
        <img class="icon-button" src={showAll ? "assets/images/file.png": "assets/images/loading.png"} alt="">
      </button>
      
      <button on:click={nextStudent} class="action-button">
        <img class="icon-button" src="assets/images/next-button.png" alt="">
      </button>
    </div>
  
    {#await studentPromise}
    Carregando seus estudantes...
      
    {:then _}
    {#if students.length != 0}
 
    <div class="student">
      <div class="circle-avatar"><img class="avatar" src="assets/images/student-male.png" alt=""></div>
      <div class="column-student">
        <p>Id: {students[index].itemId} </p>
        <p>Matricula: <input type="number" bind:value={ students[index].item.studentNumber }></p>
        <p>Nome:  <input type="text" bind:value={ students[index].item.name }></p>
        <p>Data de Nascimento: <input type="date" bind:value={ students[index].item.birthday }></p>
    
      </div>
    </div>

   {#if showAll}
   {#each students as {item, itemId}, index }

   <div class="separator"></div>
   <div class="student">
     <div class="circle-avatar"><img class="avatar" src="assets/images/student-male.png" alt=""></div>
     <div class="column-student">
       <p>Id: {itemId} </p>
       <p>Matricula: { item.studentNumber }</p>
       <p>Nome: {item.name }</p>
       <p>Data de Nascimento:{item.birthday}</p>
   
     </div>
   </div>
     
   {/each}
     
   {/if}

    {:else}
    <p>Você ainda não cadastrou nenhum estudante.</p>

      
    {/if}

  
    {:catch error} Algo deu errado :( ...
      
    {/await}

    <button class="signout-btn" on:click={signOut}>Sair</button>
  
{/if}
{:catch error}
Algo deu errado :( { error } 
  
{/await}
 

 
  

  <footer>
    <p>Aluno: Victor Stevan J Ribeiro</p>
    <p>Meu <a href="https://github.com/victorstevan" target="_blank">GitHub</a></p>
    <p>Ignore qualquer bug, esse webapp foi feito em menos de 1 hora como experiemento</p>
    <p>por tanto nenhum cuidado especial foi tomado</p>
  </footer>
</main>





<style>
  main {
    text-align: center;
    padding: 1em;
    max-width: 240px;
    margin: 0 auto;
    display: flex;
    flex-direction: column;
    
  }

  .login{
    align-self: center;
    margin-top: 30px;
    margin-bottom: 30px;
    width: 300px;
    height: 300px;
    border: 1px solid black;
    border-radius: 25px;
  }

  .circle-avatar{
    border-radius: 100%;
    width: 100px;
    height: 100px;
    border: solid 1px black;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .avatar{
    width: 70px;
    height: 70px;
  }

  .column-student{
    text-align: left;
  }

  .student{
    display: flex;
    align-self: center;
    height: 240px;
    align-items: center;
    border: 1px solid black;
    width: 500px;
    justify-content: space-evenly;
  }



  footer {
    bottom: 0;
  }

  h1 {
    color: blue;
    text-transform: uppercase;
    font-size: 4em;
    font-weight: 100;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }

  .buttons{
    display: flex;
    align-items: center;
    justify-content: center;
    column-gap: 5%;
    align-self: center;
    width: 30%;
    height: 120px;
    
  }
  .separator{
    height: 30px;
  }

  .icon-button{
    width: 50px;
    height: 50px;
  }

  .signout-btn{
    margin-top: 50px;
    margin-bottom: 50px;
    height: 100px;
    align-self: center;
    width: 150px;
  }
</style>




