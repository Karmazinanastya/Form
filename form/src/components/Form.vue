<template>
    <div class="container mt-5">
        <form @submit.prevent="submitForm" class="mb-3">
            <div>
                <h2>Registration</h2>
            </div>
            <div class="mb-3">
                <label for="email" class="form-label text-start">Email:</label>
                <input type="email" class="form-control" id="email" v-model="formData.email" required>
                <div v-if="!isValidEmail" class="text-danger">Please enter a valid email address</div>
            </div>
            <div class="mb-3">
                <label for="password" class="form-label text-start">Password:</label>
                <input type="password" class="form-control" id="password" v-model="formData.password" required>
            </div>
            <div class="mb-3">
                <label for="lastName" class="form-label text-start">Last name:</label>
                <input type="text" class="form-control" id="lastName" v-model="formData.lastName" required>
            </div>
            <div class="mb-3">
                <label for="firstName" class="form-label text-start">Name:</label>
                <input type="text" class="form-control" id="firstName" v-model="formData.firstName" required>
            </div>
            <div class="mb-3">
                <label for="middleName" class="form-label text-start">Middle name:</label>
                <input type="text" class="form-control" id="middleName" v-model="formData.middleName" required>
            </div>
            <div class="mb-3">
                <label for="dob" class="form-label text-start">Date of birth:</label>
                <input type="date" class="form-control" id="dob" v-model="formData.dob" required>
            </div>
            <div class="mb-3">
                <label for="group" class="form-label text-start">Group selection:</label>
                <select class="form-select" id="group" v-model="formData.group">
                    <option value="" disabled selected>Select a group</option>
                    <option value="Група 1">Group 1</option>
                    <option value="Група 2">Group 2</option>
                    <option value="Група 3">Group 3</option>
                </select>
            </div>
            <div class="mb-3">
                <label for="file" class="form-label text-start">File:</label>
                <input type="file" class="form-control" id="file">
            </div>
            <div class="mb-3">
                <label class="form-label text-start">Gender:</label><br>
                <div class="form-check form-check-inline">
                    <input class="form-check-input" type="radio" name="gender" id="male" value="male" v-model="formData.gender" required>
                    <label class="form-check-label" for="male">Male</label>
                </div>
                <div class="form-check form-check-inline">
                    <input class="form-check-input" type="radio" name="gender" id="female" value="female" v-model="formData.gender" required>
                    <label class="form-check-label" for="female">Female</label>
                </div>
            </div>
            <div class="mb-3">
                <label for="phone" class="form-label text-start">Phone number:</label>
                <input ref="phoneInput"
                       type="tel"
                       class="form-control"
                       id="phone"
                       v-model="formData.phone"
                       :mask="phoneMask"
                       required
                       placeholder="+38(099) 999-99-99" >
                <div v-if="!isValidPhone" class="text-danger">Please enter a valid phone number</div>
            </div>
            <button type="submit" class="btn btn-primary">Save</button>
        </form>

        <table class="table">
            <thead>
                <tr>
                    <th scope="col">Email</th>
                    <th scope="col">Last name</th>
                    <th scope="col">Name</th>
                    <th scope="col">Middle name</th>
                    <th scope="col">Date of birth</th>
                    <th scope="col">Gender</th>
                    <th scope="col">Phone number</th>
                    <th scope="col">Actions</th>
                </tr>
            </thead>
            <tbody id="table-body">
                <tr v-for="(data, index) in tableData" :key="index">
                    <td>{{ data.email }}</td>
                    <td>{{ data.lastName }}</td>
                    <td>{{ data.firstName }}</td>
                    <td>{{ data.middleName }}</td>
                    <td>{{ data.dob }}</td>
                    <td>{{ data.gender }}</td>
                    <td>{{ data.phone }}</td>
                    <td>
                        <button @click="duplicateRow(index)">Duplicate</button>
                        <button @click="deleteRow(index)">Delete</button>
                    </td>
                </tr>
            </tbody>
        </table>
        <div>
            <button id="delete-selected" @click="deleteSelectedRows" style="display: none">Delete Selected</button>
            <button id="duplicate-selected" @click="duplicateSelectedRows" style="display: none">Duplicate Selected</button>
        </div>
    </div>
</template>

<script>
    import TheMask from "vue-the-mask";

    export default {
        name: "Form",
        components: {
            TheMask,
        },
        props: {
            msg: String,
        },
        data() {
            return {
                formData: {
                    email: "",
                    password: "",
                    lastName: "",
                    firstName: "",
                    middleName: "",
                    dob: "",
                    group: "",
                    file: "",
                    gender: "",
                    phone: "",
                },
                tableData: [],
                isValidEmail: true,
                isValidPhone: true,
                phoneMask: '##(0##) ###-##-##',
                selectAll: false,
                selectedRows: [],
            };
        },
        mounted() {
            this.$refs.phoneInput.mask = this.phoneMask;
        },
        methods: {
            submitForm() {
                // Валідація електронної пошти
                if (!this.validateEmail(this.formData.email)) {
                    this.isValidEmail = false;
                } else {
                    this.isValidEmail = true;
                }

                // Валідація номера телефону
                if (!this.validatePhone(this.formData.phone)) {
                    this.isValidPhone = false;
                } else {
                    this.isValidPhone = true;
                }

                // Якщо усі дані валідні, додаємо до таблиці
                if (this.isValidEmail && this.isValidPhone) {
                    this.tableData.push({ ...this.formData });
                    this.formData = {
                        email: "",
                        password: "",
                        lastName: "",
                        firstName: "",
                        middleName: "",
                        dob: "",
                        group: "",
                        file: "",
                        gender: "",
                        phone: "",
                    };
                }
            },
            validateEmail(email) {
                const re = /\S+@\S+\.\S+/;
                return re.test(email);
            },
            validatePhone(phone) {
                const numericPhone = phone.replace(/\D/g, ""); 
                return numericPhone.length === 13; 
            },

            duplicateRow(index) {
                const newRow = { ...this.tableData[index] };
                this.tableData.splice(index + 1, 0, newRow);
            },
            deleteRow(index) {
                this.tableData.splice(index, 1);
            },
            selectAllRows() {
                if (this.selectAll) {
                    this.selectedRows = this.tableData.map((_, index) => index);
                } else {
                    this.selectedRows = [];
                }
            },
            deleteSelectedRows() {
                this.selectedRows.sort((a, b) => b - a);
                this.selectedRows.forEach((index) => {
                    this.tableData.splice(index, 1);
                });
                thisselectedRows = [];
                this.selectAll = false;
            },
            duplicateSelectedRows() {
                this.selectedRows.sort();
                this.selectedRows.forEach((index) => {
                    const newRow = { ...this.tableData[index] };
                    this.tableData.splice(index + 1, 0, newRow);
                });
                this.selectedRows = [];
                this.selectAll = false;
            },
        },

    };
</script>
