<template>
   <form @submit="exportCdr">
         <h3>CDR Exporting Tools</h3>
        <div v-show="isResult">
                <Result @go-back="resetForm" :response="response"></Result> <!-- Show Result component conditionally based on isResult -->
        </div>
        <ul>
            <li v-for="error in errors">{{ error }}</li>
        </ul>
        <div v-show="!isResult">
        <div class="form-group mt-5" :class="{ error: v$.linkcodes.$errors.length }">
                    <label class="form-label" for="exportType">Links code</label>
                    <textarea class="form-control" v-model="linkcodes" name="links" id="links" cols="10" rows="5" required></textarea>
                </div>

                    <div class="form-group mt-5">
                        <label class="form-label" for="exportFormat">Start Date:</label>
                        <input type="date" class="form-control" v-model="startDate" id="startDate" name="startDate" required>
                    </div>

                    <div class="form-group mt-5">
                        <label class="form-label" for="exportFormat">End Date:</label>
                        <input type="date" class="form-control" v-model="endDate" id="endDate" name="endDate" required>
                    </div>

                <div class="form-group mt-5">
                    <button type="submit" class="btnContactSubmit" :disabled="processing">
                        <span v-if="processing">
                             <!-- Spinner component or HTML for spinner -->
                                <!-- <i class="bi bi-arrow-repeat" style="font-size: 20px;"></i> 
                                Processing... -->
                                <i class="fa-solid fa-circle-notch fa-spin"></i> Loading....
                                <!-- <i class="fa-solid fa-rotate-right"></i> -->
                        </span>
                        <span v-else>
                             Export Now !!
                        </span>
                    </button>
                </div>
        </div>
         
         
    </form>
</template>

<script>
import Result from './Result.vue';
import axios from 'axios';
import { useVuelidate } from '@vuelidate/core'
import { required, email } from '@vuelidate/validators'


export default {
    name: 'CDRForm',
    setup() {
        const v$ = useVuelidate();
        return { v$ };
    },
    components: {
        Result
    },
    data() {
        return {
            isResult: false, // Initially set to true to show the success message
            linkcodes: '',
            linkcodesArray: [],
            startDate: '',
            response: {}, // To hold the response from the API
            endDate: new Date(), // To hold the end date,
            processing: false, // To manage loading state if needed
            errors: [] // To hold any error messages
        };
    },
    validations: {
        linkcodes: { required },
        startDate: { required },
        endDate: { required }
    },
    methods: {
       async  exportCdr(event) {
            event.preventDefault();
            this.processing = true; 
            // Simulate an API call or processing logic
            try {  
                 this.linkcodesArray = this.linkcodes.split('\n').map(code => code.trim()).filter(code => code); 
                 const response = await axios.post('http://127.0.0.1:10000/api/', {
                    "linkcodes": this.linkcodesArray,
                    "fromDate": this.startDate,
                    "endDate": this.endDate,
                    "exportType": "0"
                }, {
                    headers: {
                        'Content-Type': 'application/json',
                        'Access-Control-Allow-Origin': '*', // Ensure CORS is handled if needed
                        'Accept': 'application/json'
                    }
                    });
                
                if (response.status !== 200) {
                                        throw new Error('Failed to export CDR'); }
                this.response = response.data; // Store the response data

                this.isResult = true; // Show the result component on success
            } catch (error) {
                console.error('Error exporting CDR:', error);
                this.isResult = true; // Show error message if needed
            } finally {
                this.processing = false; // Reset processing state
            }
        },
        resetForm() {
            this.isResult = false;
            window.location.reload(); // Reset the form by reloading the page
        }
    },
    mounted() {
        // You can perform any initialization logic here if needed
        const currentDate = new Date() // Set default start date to today .toISOString().split('T')[0];
        const thirthDay = currentDate.getDate() - 30; // Calculate the date 30 days ago
        // Set start date to 30 days ago
        // this.startDate = new Date(currentDate.setDate(thirthDay)).toISOString().split('T')[0];
        this.startDate = new Date(currentDate.setDate(thirthDay)).toISOString().split('T')[0];
        // this.startDate =  // Set start date to today
        this.endDate = new Date().toISOString().split('T')[0]; // Set
    }
};
</script>

