<template>
    <div class="task-container">
        <b-row>
            <b-col>
                <b-button @click="back" block>
                    Return Back
                </b-button>
            </b-col>
        </b-row>
        <b-row v-if="typeof professor.user !== 'undefined'">
            <b-col sm="8">
                <b-card class="task-card" no-body>
                    <template v-slot:header>
                        <h5 class="mb-0">
                            <b-row>
                                <b-col sm="8">
                                    <strong>{{ptask.title}}</strong>
                                </b-col>
                                <b-col sm="3">
                                    <b-badge :variant="
                            daysBeforeDeadline > 7 ? 'success' : (daysBeforeDeadline >= 2 ? 'warning' : 'danger')">
                                        {{daysBeforeDeadline + ' days '
                                        + (daysBeforeDeadline > 0 ? 'before' : 'after')
                                        + ' deadline'}}
                                    </b-badge>
                                </b-col>
                            </b-row>
                        </h5>
                    </template>

                    <b-card-body>
                        <b-row>
                            <b-col sm="3">
                                <div class="task-card-elem text-center">
                                    <strong><i>Professor</i></strong>
                                </div>
                            </b-col>
                            <b-col sm="9">
                                <div class="task-card-elem text-center">
                                    {{professor.user.firstName + ' ' + professor.user.secondName}}
                                </div>
                            </b-col>
                        </b-row>
                        <b-row>
                            <b-col sm="3">
                                <div class="task-card-elem text-center">
                                    <strong><i>Description</i></strong>
                                </div>
                            </b-col>
                            <b-col sm="9">
                                <div class="task-card-elem text-center">
                                    {{ptask.description}}
                                </div>
                            </b-col>
                        </b-row>
                        <b-row>
                            <b-col sm="3">
                                <div class="task-card-elem text-center">
                                    <strong><i>Due date</i></strong>
                                </div>
                            </b-col>
                            <b-col sm="9">
                                <div class="task-card-elem text-center">
                                    {{ptask.dueDate.slice(0, 19).replace('T', ' ')}}
                                </div>
                            </b-col>
                        </b-row>
                        <b-row>
                            <b-col sm="3">
                                <div class="task-card-elem text-center">
                                    <strong><i>Priority</i></strong>
                                </div>
                            </b-col>
                            <b-col sm="9">
                                <div class="task-card-elem text-center">
                                    {{ptask.priority}}
                                </div>
                            </b-col>
                        </b-row>
                        <b-row>
                            <b-col sm="3">
                                <div class="task-card-elem text-center">
                                    <strong><i>Type</i></strong>
                                </div>
                            </b-col>
                            <b-col sm="9">
                                <div class="task-card-elem text-center">
                                    {{ptask.type.replace('_', ' ')}}
                                </div>
                            </b-col>
                        </b-row>
                        <b-row>
                            <b-col sm="3">
                                <div class="task-card-elem text-center">
                                    <strong><i>Status</i></strong>
                                </div>
                            </b-col>
                            <b-col sm="9">
                                <div class="task-card-elem text-center">
                                    {{stask.status.replace('_', ' ')}}
                                </div>
                            </b-col>
                        </b-row>
                        <b-row>
                            <b-col offset-sm="3" v-if="stask.status !== 'COMPLETED'">
                                <div class="task-card-elem text-center">
                                    <b-button block v-b-modal.update-status variant="success">
                                        Update status
                                    </b-button>
                                    <b-modal @cancel="clearErrors"
                                             @ok="updateStatus"
                                             id="update-status"
                                             title="Are you sure?"
                                    >
                                        <h5 class="task-card-elem">
                                            Are you sure, that you want to update the status of the task?
                                            In this case, you will not have an ability to reset the status of the
                                            task and notification will be sent to the creator of this task.
                                        </h5>
                                    </b-modal>
                                </div>
                            </b-col>
                        </b-row>
                        <b-row>
                            <b-col sm="3">
                                <div class="task-card-elem text-center">
                                    <strong><i>Assessment</i></strong>
                                </div>
                            </b-col>
                            <b-col sm="9">
                                <div class="task-card-elem text-center">
                                    {{stask.assessment.replace('_', ' ')}}
                                </div>
                            </b-col>
                        </b-row>
                        <b-row>
                            <b-col sm="3">
                                <div class="task-card-elem text-center">
                                    <strong><i>Common attachments</i></strong>
                                </div>
                            </b-col>
                            <b-col>
                                <b-row v-if="commonAttachments.length === 0">
                                    <b-col>
                                        <div class="task-card-elem text-center">
                                            <strong>Empty</strong>
                                        </div>
                                    </b-col>
                                </b-row>
                                <b-row v-bind:key="a.id" v-for="a in commonAttachments">
                                    <b-col>
                                        <div class="task-card-elem text-center">
                                            <b-button :href="a.endpointUrl + '/' + a.fileBucket + '/' + a.fileKey">
                                                {{a.fileKey}}
                                            </b-button>
                                        </div>
                                    </b-col>
                                </b-row>
                            </b-col>
                        </b-row>
                        <b-row>
                            <b-col sm="3">
                                <div class="task-card-elem text-center">
                                    <strong><i>Local attachments</i></strong>
                                </div>
                            </b-col>
                            <b-col sm="9">
                                <b-row v-if="localAttachments.length === 0">
                                    <b-col>
                                        <div class="task-card-elem text-center">
                                            <strong>Empty</strong>
                                        </div>
                                    </b-col>
                                </b-row>
                                <b-row v-bind:key="a.id" v-for="a in localAttachments">
                                    <b-col sm="9">
                                        <div class="task-card-elem text-center">
                                            <b-button
                                                    :href="a.endpointUrl + '/' + a.fileBucket + '/' + a.fileKey">
                                                {{a.fileKey}}
                                            </b-button>
                                        </div>
                                    </b-col>
                                    <b-col sm="3">
                                        <div class="task-card-elem text-center">
                                            <b-button block v-b-modal="'delete-attach-' + a.fileKey" variant="danger">
                                                Delete
                                            </b-button>
                                            <b-modal :id="'delete-attach-' + a.fileKey" @cancel="clearErrors"
                                                     @ok="() => removeAttachment(a.id)" cancel-variant="danger"
                                                     centered ok-variant="success"
                                                     ref="modal"
                                                     title="Are you sure?"
                                            >
                                                Are you sure that you want to delete this attachment?
                                                <b-alert show v-if="attachmentError !== ''" variant="danger">
                                                    {{attachmentError}}
                                                </b-alert>
                                            </b-modal>
                                        </div>
                                    </b-col>
                                </b-row>
                                <b-row>
                                    <b-col sm="8">
                                        <div class="task-card-elem">
                                            <b-form-file
                                                    :state="Boolean(newAttachmentFile)"
                                                    drop-placeholder="Drop file here..."
                                                    placeholder="Choose a file"
                                                    v-model="newAttachmentFile"
                                            />
                                        </div>
                                    </b-col>
                                    <b-col sm="4">
                                        <div class="task-card-elem text-center">
                                            <b-button @click="addAttachment" v-if="!isUploading"
                                                      variant="success">
                                                Upload
                                            </b-button>
                                            <b-spinner label="Spinning" v-if="isUploading" variant="success"/>
                                        </div>
                                    </b-col>
                                </b-row>
                                <b-row>
                                    <b-col>
                                        <div class="text-center">
                                            <b-alert show v-if="attachmentError !== ''" variant="danger">
                                                {{attachmentError}}
                                            </b-alert>
                                        </div>
                                        <div class="text-center">
                                            <b-alert show v-if="fetchError !== ''" variant="danger">
                                                {{fetchError}}
                                            </b-alert>
                                        </div>
                                    </b-col>
                                </b-row>
                            </b-col>
                        </b-row>
                    </b-card-body>
                </b-card>
            </b-col>
            <b-col sm="4">
                <b-card class="task-card" no-body>
                    <template v-slot:header>
                        <h5 class="mb-0">
                            <b-row>
                                <b-col>
                                    <strong>Messages</strong>
                                </b-col>
                            </b-row>
                        </h5>
                    </template>

                    <b-card-body>
                        <b-row v-if="messages.length === 0">
                            <b-col>
                                <div class="task-card-elem text-center">
                                    <strong>No messages yet</strong>
                                </div>
                            </b-col>
                        </b-row>
                        <div class="message-overflow-container" v-if="messages.length !== 0">
                            <b-row v-bind:key="m.id" v-for="m in messagesSortedByDate">
                                <b-col :offset-sm="m.sender === professor.user.id ? 0 : 3" sm="9">
                                    <div class="message">
                                        <b-row>
                                            <b-col>
                                                <div class="message-container">
                                                    {{m.text}}
                                                </div>
                                            </b-col>
                                        </b-row>
                                        <b-row>
                                            <b-col>
                                                <div class="message-container-label text-center">
                                                    {{m.sentDate.slice(0, 19).replace('T', ' ')
                                                    + " by "
                                                    + (m.sender === professor.user.id
                                                    ? professor.user.firstName + ' ' + professor.user.secondName
                                                    : "you")}}
                                                </div>
                                            </b-col>
                                        </b-row>
                                    </div>
                                </b-col>
                            </b-row>
                        </div>
                        <b-row>
                            <b-col sm="8">
                                <div class="task-card-elem">
                                    <b-form-textarea
                                            id="messageArea"
                                            max-rows="6"
                                            rows="3"
                                            v-model="tempMessage"
                                    />
                                </div>
                            </b-col>
                            <b-col sm="4">
                                <div class="task-card-elem text-center">
                                    <b-spinner label="Spinning" v-if="isSending" variant="primary"/>
                                    <b-button @click="addMessage" block pill v-else variant="primary">
                                        Send
                                    </b-button>
                                </div>
                            </b-col>
                        </b-row>
                        <b-row>
                            <b-col>
                                <div class="text-center">
                                    <b-alert show v-if="addError !== ''" variant="danger">
                                        {{addError}}
                                    </b-alert>
                                </div>
                            </b-col>
                        </b-row>
                    </b-card-body>
                </b-card>
            </b-col>
        </b-row>
    </div>
</template>

<script>
	import axios from "axios";
	import {API_SERVER_PATH} from "@/utils/constants";

	export default {
		name: "StudentTaskView",
		data() {
			return {
				studentTaskId: this.$route.params.studentTaskId,
				professorTaskId: this.$route.params.professorTaskId,
				currentUserId: this.$store.getters['security/userId'],
				currentUserToken: this.$store.getters['security/token'],
				professor: {},
				ptask: {},
				stask: {},
				subject: {},
				messages: [],
				commonAttachments: [],
				localAttachments: [],
				tempMessage: '',
				newAttachmentFile: null,
				isUploading: false,
				isUpdating: false,
				isSending: false,
				polling: null,
				fetchError: '',
				updateError: '',
				addError: '',
				attachmentError: ''
			}
		},
		computed: {
			daysBeforeDeadline() {
				return Math.round(
					Math.abs(
						(new Date(this.ptask.dueDate).getTime()
							- new Date().getTime()
						) / (24 * 3600 * 1000)
					)
				);
			},
			messagesSortedByDate() {
				return this.messages.slice().sort(function (a, b) {
					return new Date(a.sentDate).getTime() - new Date(b.sentDate).getTime();
				})
			}
		},
		methods: {
			pollData: function () {
				this.polling = setInterval(() => {
					this.fetchData();
				}, 10000);
			},
			back: function () {
				this.$router.go(-1);
			},
			fetchData: function () {
				this.clearNotifications();
				this.fetchTaskLocalAttachments();
				this.fetchTaskCommonAttachments();
				this.fetchTaskMessages();
				this.fetchProfessorTaskSubject();
				this.fetchStudentTaskProfessor();
				this.fetchStudentTask();
				this.fetchProfessorTask();
			},
			clearNotifications: function () {
				axios.get(API_SERVER_PATH + `/stask/${this.studentTaskId}/notifications/${this.currentUserId}/discard`, {
					headers: {
						'Authorization': 'Bearer ' + this.currentUserToken
					}
				});
			},
			updateStatus: function () {
				this.isUpdating = true;
				axios({
					method: 'put',
					params: {
						status: 'COMPLETED'
					},
					url: API_SERVER_PATH + `/stask/${this.studentTaskId}/status/update`,
					headers: {
						'Authorization': 'Bearer ' + this.$store.getters['security/token']
					}
				}).then(() => {
					this.clearErrors();
					this.fetchData();
					this.isUpdating = false;
				}).catch((error) => {
					if (error.response) {
						this.updateError = error.response.data;
					} else if (error.request) {
						this.updateError = "Server does not respond.";
					}
					this.isUpdating = false;
				});
			},
			addMessage: function () {
				this.isSending = true;
				axios.post(API_SERVER_PATH + `/stask/${this.studentTaskId}/message/add`, {
					text: this.tempMessage,
					sentDate: new Date(),
					sender: this.$store.getters['security/userId'],
					receiver: this.professor.user.id,
					studentTask: this.studentTaskId
				}, {
					headers: {
						'Authorization': 'Bearer ' + this.$store.getters['security/token']
					}
				}).then(() => {
					this.clearErrors();
					this.fetchData();
					this.isSending = false;
					this.tempMessage = '';
				}).catch((error) => {
					if (error.response) {
						this.addError = error.response.data['text'];
					} else if (error.request) {
						this.addError = "Server does not respond.";
					}
					this.isSending = false;
				});
			},
			addAttachment: function () {
				this.isUploading = true;
				let fileData = new FormData();
				fileData.append("file", this.newAttachmentFile);

				axios.post(API_SERVER_PATH + `/stask/${this.studentTaskId}/attachment/add`, fileData, {
					headers: {
						'Authorization': 'Bearer ' + this.$store.getters['security/token'],
						'Content-Type': 'multipart/form-data'
					}
				}).then(() => {
					this.clearErrors();
					this.fetchData();
					this.isUploading = false;
				}).catch((error) => {
					if (error.response) {
						this.attachmentError = error.response.data['message'] || error.response.data;
					} else if (error.request) {
						this.attachmentError = "Server does not respond.";
					}
					this.isUploading = false;
				});
			},
			removeAttachment: function (attachmentId) {
				this.attachmentError = "";
				axios.delete(API_SERVER_PATH + `/stask/${this.studentTaskId}/attachment/${attachmentId}/delete`, {
					headers: {
						'Authorization': 'Bearer ' + this.$store.getters['security/token']
					}
				}).then(() => {
					this.clearErrors();
					this.fetchData();
				}).catch((error) => {
					if (error.response) {
						this.attachmentError = error.response.data;
					} else if (error.request) {
						this.attachmentError = "Server does not respond.";
					}
				});
			},
			fetchStudentTaskProfessorUser: function (profId) {
				axios.get(API_SERVER_PATH + `/professor/${profId}/user`, {
					headers: {
						'Authorization': 'Bearer ' + this.$store.getters['security/token']
					}
				}).then((response) => {
					this.clearErrors();
					this.professor = Object.assign({}, this.professor, {
						user: response.data
					});
				}).catch((error) => {
					if (error.response) {
						this.fetchError = error.response.data;
					} else if (error.request) {
						this.fetchError = "Server does not respond.";
					}
				});
			},
			fetchStudentTaskProfessor: function () {
				axios.get(API_SERVER_PATH + `/stask/${this.studentTaskId}/professor`, {
					headers: {
						'Authorization': 'Bearer ' + this.$store.getters['security/token']
					}
				}).then((response) => {
					this.clearErrors();
					this.professor = Object.assign({}, this.professor, {
						id: response.data.id
					});
					this.fetchStudentTaskProfessorUser(response.data.id);
				}).catch((error) => {
					if (error.response) {
						this.fetchError = error.response.data;
					} else if (error.request) {
						this.fetchError = "Server does not respond.";
					}
				});
			},
			fetchStudentTask: function () {
				axios.get(API_SERVER_PATH + `/stask/${this.studentTaskId}/get`, {
					headers: {
						'Authorization': 'Bearer ' + this.$store.getters['security/token']
					}
				}).then((response) => {
					this.clearErrors();
					this.stask = Object.assign({}, response.data);
				}).catch((error) => {
					if (error.response) {
						this.fetchError = error.response.data;
					} else if (error.request) {
						this.fetchError = "Server does not respond.";
					}
				});
			},
			fetchTaskMessages: function () {
				axios.get(API_SERVER_PATH + `/stask/${this.studentTaskId}/messages`, {
					headers: {
						'Authorization': 'Bearer ' + this.$store.getters['security/token']
					}
				}).then((response) => {
					this.clearErrors();
					this.messages = response.data;
				}).catch((error) => {
					if (error.response) {
						this.fetchError = error.response.data;
					} else if (error.request) {
						this.fetchError = "Server does not respond.";
					}
				});
			},
			fetchTaskLocalAttachments: function () {
				axios.get(API_SERVER_PATH + `/stask/${this.studentTaskId}/attachments`, {
					headers: {
						'Authorization': 'Bearer ' + this.$store.getters['security/token']
					}
				}).then((response) => {
					this.clearErrors();
					this.localAttachments = response.data;
				}).catch((error) => {
					if (error.response) {
						this.fetchError = error.response.data;
					} else if (error.request) {
						this.fetchError = "Server does not respond.";
					}
				});
			},
			fetchTaskCommonAttachments: function () {
				axios.get(API_SERVER_PATH + `/ptask/${this.professorTaskId}/attachments`, {
					headers: {
						'Authorization': 'Bearer ' + this.$store.getters['security/token']
					}
				}).then((response) => {
					this.clearErrors();
					this.commonAttachments = response.data;
				}).catch((error) => {
					if (error.response) {
						this.fetchError = error.response.data;
					} else if (error.request) {
						this.fetchError = "Server does not respond.";
					}
				});
			},
			fetchProfessorTaskSubject: function () {
				axios.get(API_SERVER_PATH + `/ptask/${this.professorTaskId}/subject`, {
					headers: {
						'Authorization': 'Bearer ' + this.$store.getters['security/token']
					}
				}).then((response) => {
					this.clearErrors();
					this.subject = Object.assign({}, response.data);
				}).catch((error) => {
					if (error.response) {
						this.fetchError = error.response.data;
					} else if (error.request) {
						this.fetchError = "Server does not respond.";
					}
				});
			},
			fetchProfessorTask: function () {
				axios.get(API_SERVER_PATH + `/ptask/${this.professorTaskId}/get`, {
					headers: {
						'Authorization': 'Bearer ' + this.$store.getters['security/token']
					}
				}).then((response) => {
					this.clearErrors();
					this.ptask = Object.assign({}, response.data);
				}).catch((error) => {
					if (error.response) {
						this.fetchError = error.response.data;
					} else if (error.request) {
						this.fetchError = "Server does not respond.";
					}
				});
			},
			clearErrors: function () {
				this.fetchError = '';
				this.updateError = '';
				this.attachmentError = '';
			}
		},
		mounted() {
			this.fetchData();
			this.pollData();
		},
		beforeDestroy() {
			clearInterval(this.polling);
			this.clearNotifications();
		}
	}
</script>

<style scoped>
    .task-container {
        margin: 20px;
        padding: 20px 20px;
        background-color: white;
    }

    .task-card {
        margin: 10px;
        padding: 5px;
    }

    .task-card-elem {
        margin: 5px;
        padding: 10px;
        font-size: 22px;
        border-radius: 10px;
        background-color: whitesmoke;
    }

    .message-container {
        background-color: whitesmoke;
        font-size: 20px;
        margin: 5px;
        padding: 10px;
        border-radius: 20px;
    }

    .message-container-label {
        background-color: whitesmoke;
        font-size: 11px;
        margin: 5px;
        padding: 5px;
        border-radius: 20px;
    }

    .message {
        background-color: lightgray;
        border-radius: 10px;
        margin: 5px;
        padding: 5px;
    }

    .message-overflow-container {
        overflow: scroll;
        height: 540px;
        width: 100%;
        overflow-x: hidden;
    }
</style>
