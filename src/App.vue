<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

const todos_asc = computed(() => todos.value.sort((a,b) =>{
	return a.createdAt - b.createdAt
}))

watch(name, (newVal) => {
	localStorage.setItem('name', newVal)
})

watch(todos, (newVal) => {
	localStorage.setItem('todos', JSON.stringify(newVal))
}, {
	deep: true
})

const addListaTarefas = () => {
	if (input_content.value.trim() === '' || input_category.value === null) {
		return
	}

	todos.value.push({
		content: input_content.value,
		category: input_category.value,
		done: false,
		editable: false,
		createdAt: new Date().getTime()
	})
}

const removerTarefa = (todo) => {
	todos.value = todos.value.filter((t) => t !== todo)
}

onMounted(() => {
	name.value = localStorage.getItem('name') || ''
	todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
	<main class="app">
		
		<section class="saudacoes">
			<h2 class="titulo">
				Olá , <input type="text" id="name" placeholder="Digite Seu nome" v-model="name">
			</h2>
		</section>

		<section class="criar-tarefa">
			<h3>CRIE SUA LISTA DE TAREFAS</h3>

			<form id="new-todo-form" @submit.prevent="addListaTarefas">
				<h4>O que esta na sua lista de terefas?</h4>
				<input 
					type="text" 
					name="content" 
					id="content" 
					placeholder="Ex: fazer sopa"
					v-model="input_content" />
				
				<h4>Selecionar a categoria</h4>
				<div class="opcoes">

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category1" 
							value="trabalho"
							v-model="input_category" />
						<span class="bubble trabalho"></span>
						<div>Trabalho</div>
					</label>

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category2" 
							value="pessoal"
							v-model="input_category" />
						<span class="bubble pessoal"></span>
						<div>Pessoal</div>
					</label>

				</div>

				<input type="submit" value="Adicionar tarefa" />
			</form>
		</section>

		<section class="listaDeTarefas-listar"> 
			<h3>Lista de terfas</h3>
			<div class="listar" id="listaDeTarefas-listar">

				<div v-for="todo in todos_asc" :class="`listaDeTarefas-item ${todo.done && 'done'}`">
					<label>
						<input type="checkbox" v-model="todo.done" />
						<span :class="`bubble ${
							todo.category == 'trabalho' 
								? 'trabalho' 
								: 'pessoal'
						}`"></span>
					</label>

					<div class="listaDeTarefas-content">
						<input type="text" v-model="todo.content" />
					</div>

					<div class="acoes">
						<button class="deletar" @click="removerTarefa(todo)">Deletar</button>
					</div>
				</div>

			</div>
		</section>

	</main>
</template>
