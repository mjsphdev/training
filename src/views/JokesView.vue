<script setup>
import { ref, reactive, computed } from 'vue'

// CLASS **JOKE
let ctrId = 0;
class Joke {
    constructor(line1 = '', line2 = '', id = 0) {

        this.line1 = line1;
        this.line2 = line2;

        if (id == 0) {
            ctrId ++;
            this.id = ctrId;
        }
        else {
            this.id = id;

            if (ctrId < id) {
                ctrId = id
            }
        }
       
    }
}

// JOKES LIST REF
const jokesList = ref([
    new Joke('How do construction workers party?', 'They raise the roof.'),
    new Joke(
        'Why do front end developers eat lunch alone?',
        `Because they don't know how to join tables.`
    ),
    new Joke(
        'What did the grape say when it was stepped on?',
        'Nothing, it just let out a little wine.'
    ),
]);


const jokeId = ref();
const newLine1 = ref("");
const newLine2 = ref("");
const isUpdate = ref(false);
const isDelete = ref(false);
const msgStatus = ref(false);
const msg = ref("");

// SAVE OR UPDATE
const saveJoke = () => {
    if(isUpdate.value){
        const elementOfIndex = jokesList.value.findIndex((joke => joke.id == jokeId.value));
        jokesList.value[elementOfIndex].line1 = newLine1.value
        jokesList.value[elementOfIndex].line2 = newLine2.value
        msg.value = "updated";
        isUpdate.value = false
    }
    else{
        jokesList.value.push(
             new Joke(newLine1.value, newLine2.value)
        )
        msg.value = "saved";
    }  
    msgStatus.value = true
    newLine1.value = ""
    newLine2.value = ""
}

// GET DATA TO UPDATE
const updateJoke = (i, l1, l2) => {
    jokeId.value = i
    newLine1.value = l1
    newLine2.value = l2
    isUpdate.value = true
    isDelete.value = true
}

// DELETE
const deleteJoke = () => {
    const deleteUsingIndex = jokesList.value.findIndex((joke => joke.id == jokeId.value))
    jokesList.value.splice(deleteUsingIndex, 1)
    msgStatus.value = true
    msg.value = "deleted"
    newLine1.value = ""
    newLine2.value = ""
    isUpdate.value= false
}

</script>

<template>
    <div class="container">
     <div class="card shadow-lg p-5 mt-5 mb-5 bg-white rounded">
        <div 
            v-if="msgStatus" 
            class="alert alert-dismissible fade show" 
            :class="[msg === 'deleted' ? 'alert-danger' : 'alert-success']"
            role="alert">
        <strong>Success!</strong> Joke has been {{ msg }}.
            <button type="button" class="btn-close" data-bs-dismiss="alert" aria-label="Close"></button>
        </div>
        <h1 class="mt-2">Jokes Here!</h1>
        <p v-if="!jokesList.length" class="display-6"> No jokes for today!</p>
        <div class="accordion accordion-flush" id="accordionFlushExample">
             <div class="accordion-item" v-for="(jokes, index) in jokesList" :key="jokes.id">
               <h2 class="accordion-header" :id="`flush-${jokes.id}`">
                 <button 
                    class="accordion-button collapsed" 
                    type="button" 
                    data-bs-toggle="collapse" 
                    :data-bs-target="`#flush-collapse-${jokes.id}`" 
                    aria-expanded="false" 
                    :aria-controls="`flush-${jokes.id}`"
                    @click="updateJoke(jokes.id, jokes.line1, jokes.line2)"
                >
                   {{jokes.line1}}
                 </button>
               </h2>
               <div :id="`flush-collapse-${jokes.id}`" class="accordion-collapse collapse" :aria-labelledby="`flush-${jokes.id}`" data-bs-parent="#accordionFlushExample">
                 <div class="accordion-body">
                   {{jokes.line2}}
                 </div>
               </div>
             </div>
        </div>

        <h2 class="mt-3"> {{ isUpdate? 'Update' : 'Make' }} a Joke Here! </h2>
        <form
            @submit.prevent="saveJoke"
        >
            <input v-model="jokeId" type="hidden">
            <div class="mt-3">
                <label class="fw-bold">Line 1</label>
                <input v-model="newLine1" type="text" class="form-control">
            </div>
            <div class="mt-3">
                <label class="fw-bold">Line 2</label>
                <input v-model="newLine2" type="text" class="form-control">
            </div>
            <div class="row mt-3">
                <div class="col">
                    <button 
                        class="btn"
                        :class="[isUpdate ? 'btn-success' : 'btn-primary']"
                        :disabled="newLine1.length < 5 && newLine2 < 5"
                    >
                        {{ isUpdate? 'Update' : 'Save' }}
                    </button>   
                </div>
                <div class="col">
                    <button 
                        v-if="isUpdate"
                        @click="deleteJoke"
                        :disabled="jokesList.length === 0"
                        class="btn btn-outline-danger float-end"
                    >
                        Delete
                    </button>
                </div>
            </div>
        </form>
     </div>
    </div>
</template>

<style>

</style>