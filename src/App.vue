<template>
	<div>
		<header>
			<h1>Todo With Typescript</h1>
		</header>
		<main>
			<TodoInput
				:item="todoText"
				@input="updateTodoText"
				@add="addTodoItem"
			></TodoInput>
			<ul>
				<TodoListItem
					v-for="(todoItem, index) in todoItems"
					@remove="removeTodoItem"
					@toggle="toggleTodoItemDone"
					:key="index"
					:index="index"
					:todoItem="todoItem"
				></TodoListItem>
			</ul>
		</main>
		<!-- <TodoInput v-model="todoText" @add="addTodoItem"></TodoInput> -->
	</div>
</template>

<script lang="ts">
import Vue from 'vue';
import TodoInput from './components/TodoInput.vue';
import TodoListItem from './components/TodoListItem.vue';
const STORAGE_KEY = 'vue-todo-ts-v1';
const storage = {
	save(todoItems: ITodo[]) {
		const parsed = JSON.stringify(todoItems);
		localStorage.setItem(STORAGE_KEY, parsed);
	},
	fetch(): ITodo[] {
		const todoItems = localStorage.getItem(STORAGE_KEY) || '[]';
		const result = JSON.parse(todoItems);
		return result;
	},
};
export interface ITodo {
	title: string;
	done: boolean;
}
export default Vue.extend({
	components: { TodoInput, TodoListItem },
	data() {
		return {
			todoText: '',
			todoItems: [] as ITodo[],
		};
	},
	methods: {
		updateTodoText(value: string) {
			this.todoText = value;
		},
		addTodoItem() {
			const value = this.todoText;
			this.todoItems.push({ title: value, done: false });
			storage.save(this.todoItems);
			this.initTodoText();
		},
		initTodoText() {
			this.todoText = '';
		},
		fetchTodoItems() {
			this.todoItems = storage.fetch().sort((a, b) => {
				if (a.title < b.title) {
					return -1;
				}
				if (a.title > b.title) {
					return 1;
				}
				return 0;
			});
		},
		removeTodoItem(index: number) {
			console.log('index', index);
			this.todoItems.splice(index, 1);
			storage.save(this.todoItems);
		},
		toggleTodoItemDone(todoItem: ITodo, index: number) {
			this.todoItems.splice(index, 1, {
				...todoItem,
				done: !todoItem.done,
			});
			storage.save(this.todoItems);
		},
	},
	created() {
		this.fetchTodoItems();
	},
});
</script>
<style scope></style>
