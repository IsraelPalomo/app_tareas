<template>
	<div>
		<nav class="navbar navbar-light bg-light ">
			<a href="/" class="navbar-brand p-5">MEVN Stack</a>
		</nav>
		<div class="container ">
			<div class="row pt-5">
				<div class="col-md-5">
					<div class="card-body">
						<form @submit.prevent="addTask">
							<div class="form-group">
								<input
									type="text"
									placeholder="Inserta una nueva tarea"
									class="form-control"
									v-model="task.title"
								/>
							</div>
							<div class="form-group">
								<textarea
									class="form-control"
									cols="30"
									rows="10"
									placeholder="Inserte una description"
									v-model="task.description"
								></textarea>
							</div>
							<template v-if="edit === false">
								<button class="btn btn-primary btn-lg btn-block w-100">Enviar</button>
							</template>
							<template v-else>
								<button class="btn btn-primary btn-lg btn-block w-100">Actualizar</button>
							</template>
						</form>
					</div>
				</div>
				<div class="col-md-6">
					<table class="table table-bordered">
						<thead>
							<tr>
								<th>Task</th>
								<th>Description</th>
							</tr>
						</thead>
						<tbody>
							<tr v-for="(task, index) of tasks" :key="index">
								<td>{{ task.title }}</td>
								<td>{{ task.description }}</td>
								<td>
									<button @click="tasksDelete(task._id)" class="btn btn-danger">Eliminar</button>
									<button @click="updateTask(task._id)" class="btn btn-secondary">Edit</button>
								</td>
							</tr>
						</tbody>
					</table>
				</div>
			</div>
		</div>
	</div>
</template>
<script>
export default {
	data() {
		return {
			task: {
				title: "",
				description: "",
				tareaEditada: "",
			},
			tasks: [],
			edit: false,
		};
	},

	methods: {
		addTask() {
			if (this.edit === false) {
				fetch("/api/tasks/", {
					method: "POST",
					body: JSON.stringify(this.task),
					headers: {
						Accept: "application/json",
						"Content-type": "application/json",
					},
				})
					.then((res) => res.json())
					.then((data) => {
						this.getTask();
					});
			} else {
				fetch("/api/tasks/" + this.tareaEditada, {
					method: "PUT",
					body: JSON.stringify(this.task),
					headers: {
						Accept: "application/json",
						"Content-type": "application/json",
					},
				})
					.then((res) => res.json())
					.then((data) => {
						this.getTask(), (this.edit = false);
					});
			}

			this.task.title = "";
			this.task.description = "";
		},
		getTask() {
			fetch("/api/tasks/")
				.then((res) => res.json())
				.then((data) => {
					this.tasks = data;
					console.log(this.tasks);
				});
		},
		tasksDelete(id) {
			fetch("/api/tasks/" + id, {
				method: "DELETE",
				headers: {
					Accept: "application/json",
					"Content-type": "application/json",
				},
			})
				.then((res) => res.json())
				.then((data) => {
					this.getTask();
				});
		},
		updateTask(id) {
			fetch("/api/tasks/" + id)
				.then((res) => res.json())
				.then((data) => {
					this.task.title = data.title;
					this.task.description = data.description;
					this.edit = true;
					this.tareaEditada = data._id;
				});
		},
	},
	created() {
		this.getTask();
	},
};
</script>
