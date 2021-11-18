<template>
    <div class="container">
        <div class="row justify-content-center mt-5">
            <div class="col-md-6">
                <div class="card">
                    <div class="card-header">Todo</div>

                    <div class="card-body">
                        <div class="input-group">
                            <input
                                type="text"
                                class="form-control"
                                aria-label="todo"
                                aria-describedby="todo"
                                placeholder="Todo..."
                                v-model="todo_input"
                            />
                            <div class="input-group-append">
                                <button
                                    v-if="!edit_todo_id"
                                    type="button"
                                    class="btn btn-info"
                                    @click="saveTodo()"
                                >
                                    Add
                                </button>
                                <button
                                    v-else
                                    type="button"
                                    class="btn btn-info"
                                    @click="updateTodo()"
                                >
                                    Update
                                </button>
                            </div>
                        </div>

                        <button
                            type="button"
                            class="btn btn-secondary float-right my-3"
                            @click="resetTodo()"
                        >
                            Reset
                        </button>
                        <table class="table mt-3">
                            <thead>
                                <tr>
                                    <th scope="col">#</th>
                                    <th scope="col">Name</th>
                                    <th scope="col">Action</th>
                                </tr>
                            </thead>
                            <tbody>
                                <tr v-for="(todo, index) in todos" :key="index">
                                    <th scope="row">{{ index + 1 }}</th>
                                    <td>{{ todo.name }}</td>
                                    <td>
                                        <button
                                            type="button"
                                            class="btn btn-info"
                                            @click="editTodo(--index)"
                                        >
                                            Edit
                                        </button>
                                        <button
                                            type="button"
                                            class="btn btn-danger"
                                            @click="deleteTodo(--index)"
                                        >
                                            Delete
                                        </button>
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            todos: [],
            api: "http://localhost:8000/api/todos",
            todo_input: "",
            edit_todo_id: "",
            edit_index: "",
        };
    },
    mounted() {
        this.axios.get(this.api).then((res) => {
            this.todos = res.data;
        });
    },
    methods: {
        saveTodo() {
            if (this.todo_input.length > 0) {
                let data = { name: this.todo_input };
                this.axios.post(this.api + "/create", data).then((res) => {
                    this.todos.push(res.data);
                    this.todo_input = "";
                });
            }
        },
        deleteTodo(index) {
            if (this.todos[index].id) {
                this.axios
                    .delete(this.api + "/delete/" + this.todos[index].id)
                    .then((res) => {
                        this.todos.splice(index, 1);
                    });
            }
        },
        editTodo(index) {
            if (this.todos[index].id) {
                this.todo_input = this.todos[index].name;
                this.edit_todo_id = this.todos[index].id;
                this.edit_index = index;
            }
        },
        updateTodo() {
            if (this.todo_input.length > 0) {
                let data = { name: this.todo_input };
                this.axios
                    .patch(
                        this.api + "/update/" + this.todos[this.edit_index].id,
                        data
                    )
                    .then((res) => {
                        this.todos[this.edit_index].name = res.data.name;
                        this.resetTodo();
                    });
            }
        },
        resetTodo() {
            this.todo_input = "";
            this.edit_todo_id = "";
            this.edit_index = "";
        },
    },
};
</script>
