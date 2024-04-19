f<script setup>
</script>
<script>
export default{
        props: {
            pageData:{
                type : Object,
                default : (()=>{}),
            },
            pageMetadata:{
                type : Object,
                default : (()=>{}),
            }
        },
        methods : {
            
        }
    }
</script>
<template>
    <header v-if="!pageData.showSpinner" class="footer">
        <br/> 
         <div   class="container">
           <div class="col-6 col-sm-12 col-md-12 col-lg-6 col-xl-6" style="margin:auto;display: flex;margin: auto;justify-content: space-around;">
             <div v-for="header in pageMetadata.header_images">
                
             </div>
           </div>
        </div>
    </header>
</template>