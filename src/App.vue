<template>
	<h1>Todo App</h1>

	<form>
		<label for="newTodo">Enter your todo: </label>
		<input v-model="newTodo" type="text" placeholder="Enter here..." />

		<button @click.prevent="addNewTodo()">Add Todo</button>
		<button @click.prevent="deleteAllTodos()">Delete All Todos</button>

		<select v-model="filterTodoStatus" name="filterTodo" id="filterTodo">
			<option value="All">All</option>
			<option value="Completed">Completed</option>
			<option value="Incomplete">Incomplete</option>
		</select>
	</form>

	<ul>
		<li :key="index" v-for="(todo, index) in filteredTodos">
			<span :class="[todo.completed ? 'completed__todo' : '']">{{
				todo.item
			}}</span>
			<button @click.prevent="completeTodo(index)">Complete</button>
			<button @click.prevent="deleteTodo(todo.id)">Delete</button>
		</li>
	</ul>
</template>

<script lang="ts">
import { defineComponent, ref, computed } from 'vue';

interface Todo {
	id: number;
	item: string;
	completed: boolean;
}

export default defineComponent({
	name: 'TodoApp',
	setup() {
		const newTodo = ref<string>('');
		const todos = ref<Todo[]>([]);
		const filterTodoStatus = ref<string>('All');

		const filteredTodos = computed((): Todo[] => {
			const completedTodos: Todo[] = todos.value.filter(
				(todo) => todo.completed
			);
			const incompleteTodos: Todo[] = todos.value.filter(
				(todo) => !todo.completed
			);

			if (filterTodoStatus.value === 'Completed') return completedTodos;
			if (filterTodoStatus.value === 'Incomplete') return incompleteTodos;

			return todos.value;
		});

		function addNewTodo(): Todo {
			const todo: Todo = {
				id: todos.value[todos.value.length - 1]?.id + 1 || 1,
				item: newTodo.value,
				completed: false,
			};
			todos.value.push(todo);
			newTodo.value = '';

			return todo;
		}

		function completeTodo(position: number): Todo {
			let todo = todos.value[position];
			todo.completed = !todo.completed;

			return todo;
		}

		function deleteTodo(id: number): Todo[] {
			todos.value = todos.value.filter((todo) => todo.id !== id);

			return todos.value;
		}

		function deleteAllTodos(): string {
			todos.value = [];

			return 'Success';
		}

		return {
			newTodo,
			filteredTodos,
			filterTodoStatus,
			addNewTodo,
			completeTodo,
			deleteTodo,
			deleteAllTodos,
		};
	},
});
</script>

<style>
.completed__todo {
	color: green;
	text-decoration: line-through;
}
</style>
