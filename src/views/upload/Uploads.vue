<template>
    <div>
        <div class="row">
            <div class="col-lg-6 col-sm-12 offset-lg-3 mb-4">
                <b-form>
                    <b-form-group>
                        <b-form-input
                            class="mb-2 mr-sm-2 mb-sm-0 me-2"
                            placeholder="Username"
                            v-model="form.user"
                            :class="{ 'is-invalid': $v.form.user.$error }"
                        ></b-form-input>
                        <b-form-invalid-feedback>
                            <template v-if="!$v.form.user.required">
                                Username is required
                            </template>
                            <template v-if="!$v.form.user.minLength">
                                Username must have at least
                                {{
                                    $v.form.user.$params.minLength.min
                                }}
                                letters.
                            </template>
                        </b-form-invalid-feedback>
                    </b-form-group>
                    <b-form-group>
                        <b-form-file
                            v-model="form.file"
                            class="mt-3"
                            plain
                            :class="{ 'is-invalid': $v.form.user.$error }"
                            @change="onFileChange"
                        ></b-form-file>
                        <b-form-invalid-feedback>
                            <template v-if="!$v.form.file.required">
                                File is required
                            </template>
                        </b-form-invalid-feedback>
                    </b-form-group>
                    <div class="mt-2">
                        <b-button
                            variant="primary"
                            @click="submitData"
                            :disabled="isSubmitted"
                            ><b-spinner v-if="isSubmitted" small></b-spinner
                            >Upload</b-button
                        >
                    </div>
                    <div v-if="error" class="text-danger">
                        {{error}}
                    </div>
                </b-form>

            </div>
        </div>
        <div class="about mt-4">
            <h5>Uploads</h5>
            <b-table
                :items="uploads"
                :fields="fields"
                :show-empty="true"
                :busy.sync="isLoading"
            >
                <template #cell(actions)="data">
                    <b-button @click="navigateToDetails(data.item.id)"
                        >Distribution</b-button
                    >
                </template>
            </b-table>
        </div>
    </div>
</template>
<script>
import { required, minLength } from "vuelidate/lib/validators";
const initialForm = () => {  return {
                user: "",
                file: null,
            }}
export default {
    name: "Uploads",
    props: {
        msg: String,
    },
    validations: {
        form: {
            user: {
                required,
                minLength: minLength(3),
            },
            file: {
                required,
            },
        },
    },
    data() {
        return {
            uploads: [],
            fields: ["id", "user", "actions"],
            isLoading: false,
            isSubmitted:false,
            file1: null,
            form: initialForm(),
            error:''
        };
    },
    methods: {
        fetchItems() {
            this.isLoading = true
            this.$axios
                .get("http://127.0.0.1:5000/uploads")
                .then((data) => {
                    this.uploads = data.data;
                    this.isLoading = false
                })
                .catch(() => {
                    this.uploads = []
                    this.isLoading = false
                });
        },
        navigateToDetails(uploadId) {
            this.$router.push(`/uploads/${uploadId}`);
        },
        onFileChange(event) {
            this.form.file= event.target.files[0]
        },
        submitData() {
            this.$v.$touch()
            if (this.$v.$error) {
                return;
            }
            let formData = new FormData();
            formData.append('user', this.form.user)
            formData.append('file', this.form.file)
            this.uploadData(formData)
        },
        uploadData(formData) {
            this.isSubmitted = true

            this.$axios.post('http://127.0.0.1:5000/uploads',formData).then(() =>{
                    this.fetchItems()
                    this.clearForm()
                this.isSubmitted = false

            }).catch((error)=> {
                this.error = error.toString()
                this.isSubmitted = false
            })
        },
        clearForm() {
            this.$v.$reset()
            this.form = initialForm()
            this.error = ''
            
        }
            
    
    },
    mounted() {
        this.fetchItems()
    },
};
</script>

<style></style>
