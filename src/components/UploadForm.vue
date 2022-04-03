<template>
    <div class="form-container">
        <h1>Upload Form</h1>
        <div v-if="success" class="alert alert-success" role="alert">
            <p>File Upload Successfully</p>
        </div>
        <div v-if="errors.length" class="alert alert-danger" role="alert">
            <ul class="error">
                <li  v-for="error in errors" :key="errors.indexOf(error)">{{ error }}</li>
            </ul>
        </div>
        <form 
            method="post" 
            enctype="multipart/form-data" 
            @submit.prevent="uploadPhoto"
            id='uploadForm'
        >
            <div class="form-field">
                <label for="description">Description</label>
                <textarea name="description" id="description" cols="10" rows="4"></textarea>
            </div>
            <div class="form-field">
                <label for="photo">Photo Upload</label>
                <input type="file" name="photo" id="photo">
            </div>
            <button type="submit">Upload</button>
        </form>
    </div>
</template>
<script>
export default {
    name: 'UploadForm',
    data(){
        return {
            csrf_token: '', 
            errors: [],
            success: false
        }
    },
    created() {
        this.getCsrfToken();
    },
    methods:{
        uploadPhoto(){
            let uploadForm = document.getElementById('uploadForm');
            let form_data = new FormData(uploadForm);
            let self = this
            fetch("/api/upload", {
                method: 'POST',
                body: form_data,
                headers: {
                    'X-CSRFToken': this.csrf_token
                }
            })
            .then(function (response) {
                return response.json();
            })
            .then(function (data) {
                // display a success message
                console.log(data, "data");
                if('errors' in data){
                    self.errors = [...data.errors]
                    self.success = false
                } else {
                    self.errors = []
                    self.success = true
                }
            })
            .catch(function (error) {
                
                console.log(error, "error");
            });
        },

        getCsrfToken() {
            let self = this;
            fetch('/api/csrf-token')
                .then((response) => response.json())
                .then((data) => {
                console.log(data);
                self.csrf_token = data.csrf_token;
            })
        }
    }
}
</script>

<style scoped>
.form-container{
    padding: 1rem 4rem;
}
.form-field{
    margin: 1rem 0;
}

.form-field input, .form-field textarea{
    display: block;
    border: 1px solid #cccccc;
    border-radius: 6px;
}

#description{
    width: 50%;
}

button{
    padding: 10px 2rem;
    border: none;
    background: #14b8a7;
    border-radius: 6px;
    color: #fff;
}
</style>
