<template>
   <div class="container">
        <div class="container contact-form">
            <div class="contact-image">
                <!-- <img src="https://image.ibb.co/kUagtU/rocket_contact.png" alt="mtn"/> -->
                <img src="https://static.cdnlogo.com/logos/m/73/mtn-new.svg" alt="mtn"/>
            </div>
            <CdrForm />
        </div>
    </div>
</template>

<script>
 import CdrForm from './components/CdrForm.vue';
 export default {
    name: 'App',
    components: {
        CdrForm
    }
 }
</script>