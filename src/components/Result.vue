<template>
    <div>
        <div class="container" v-if="response && response.status === 'success'"> 
                <div class="row text-center">
                    <h2 style="color:#0fad00">Congratulation</h2>
                    
                    <i style="font-size:50px;color:#0fad00;" class="fa fa-solid fa-circle-check"></i>
                    <p style="font-size:16px;color:#5C5C5C;" class="mt-3">
                     File has been exported successfully. You can download it using the link below.
                    </p>
                    <div class="form-group mt-5">
                            <button class="btnContactSubmit mt-3" @click="downloadfile">
                                <span v-if="processing">
                                <!-- Spinner component or HTML for spinner -->
                                    <!-- <i class="bi bi-arrow-repeat" style="font-size: 20px;"></i> 
                                    Processing... -->
                                    <i class="fa-solid fa-circle-notch fa-spin"></i> Loading....
                                    <!-- <i class="fa-solid fa-rotate-right"></i> -->
                                </span>
                                <span v-else>
                                      Download
                                 </span>
                            </button>
                                
                    </div>
                  
                    </div>
            </div>

            <div class="container mt-5" v-if="response && response.status !== 'success'">
                 <div class="row text-center">
                    <h2 style="color:#ad0000">Sorry</h2>
                    <i style="font-size:50px;color:#ad0000;" class="fa fa-solid fa-circle-exclamation"></i>
                    <p style="font-size:16px;color:#5C5C5C;" class="mt-3">
                     File unable to export.There was an issue with the export process. Please try again later or contact support if the problem persists.
                    </p>
                </div>
            </div>             
            <div class="text-center mt-5">
                <a href="#" @click="goBack">Go back </a>
            </div>
    </div>
</template>
<script>
export default {
    name: 'Result',
    data() {
        return {
            // You can add any data properties here if needed
            // localresponse: this.response || {} ,// Initialize response from props or empty object
            processing: false // To manage loading state if needed
        };
    },
    props: {
        // You can define props here if needed
        response: {
            type: Object,
            default: () => ({})
        }
    },
    methods: {
        // You can add methods here if needed
        goBack() {
            this.$emit('go-back'); // Emit an event to notify the parent component to go back
        },
        downloadfile() {
            // Implement the logic to download the file
            // This could involve creating a link element and triggering a download
            this.processing = true; // Set processing to true while downloading
            const url = import.meta.env.VITE_API_URL
            
            fetch(`${url}fetch`, {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({ file: this.response.export_file }) // Assuming response contains fileName
            }).then(response => {
                if (response.ok) {
                    return response.blob(); // Convert the response to a Blob
                } else {
                    throw new Error('Network response was not ok.');
                }
            }).then(blob => {
                const url = window.URL.createObjectURL(blob);
                const a = document.createElement('a');
                a.href = url;
                a.download = this.response.export_file; // Set the file name for download
                document.body.appendChild(a);
                a.click();
                a.remove();
                window.URL.revokeObjectURL(url); // Clean up the URL object
            }).catch(error => {
                console.error('There was a problem with the fetch operation:', error);
            }).finally(() => {
                this.processing = false; // Reset processing state
            });

        }
    },
    mounted() {
        // You can perform any initialization logic here if needed
        // For example, you might want to fetch some initial data or set up event listeners
        if (!this.response || Object.keys(this.response).length === 0) {
            console.warn('No response data provided to Result component.');
        }
    }
    
  };
</script>

