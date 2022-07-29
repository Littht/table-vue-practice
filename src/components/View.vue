<script setup>
import { reactive } from 'vue';
import NewInput from './NewInput.vue';

let boards= reactive([
    {
        id: crypto.randomUUID(),
        name:"tablero 1",
        items:[
            {
                id:crypto.randomUUID(),
                title:"algo 1",
            },
            {
                id:crypto.randomUUID(),
                title:"algo 2",
            }
        ]
    },
    {
        id: crypto.randomUUID(),
        name:"tablero 2",
        items:[
            {
                id:crypto.randomUUID(),
                title:"algo 3",
            },
            {
                id:crypto.randomUUID(),
                title:"algo 4",
            }
        ]
    }
])
function handleNewItem(text , board){
    board.items.push(
        {
            id: crypto.randomUUID(),
            title: text.value,
        }
    )
}
function hanldeNewBoard(){
    const name = prompt("Name of the board")
    if(name !== ""){
        boards.push({  
            id: crypto.randomUUID(),
            name: name,
            items:[]
        })
    }
}
function startDrag(e, board, item){
    e.dataTransfer.setData("text/plain", JSON.stringify({boardId:board.id, itemId:item.id}))
}

function onDrop(e, dest ){
    const {boardId, itemId} = JSON.parse(e.dataTransfer.getData("text/plain"));
    const originBoard= boards.find(item => item.id === boardId);
    const originItem= originBoard.items.find(item => item.id === itemId);

    dest.items.push({...originItem});
    originBoard.items = originBoard.items.filter((item)=> item !== originItem)

}
</script>

<template>
    <nav>
        <ul>
            <li><a href="#" @click.prevent="hanldeNewBoard">Crear tabla</a></li>
        </ul>
    </nav>

    <div class="boards-container">
        <div class="boards">
            <div class="board" 
            @drop="onDrop($event, board)" 
            @dragover.prevent 
            @dragenter.prevent 
            v-for="board in boards" :key="board.id">
                <div>{{ board.name }}</div>
                <NewInput @on-new-item="(text) => handleNewItem (text , board)" />
                <div class="items">
                    <div class="item" draggable="true" @dragstart="startDrag($event, board , item)" v-for="item in board.items" :key="item.id">
                    {{item.title}}
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped>
.boards{
    display:flex;
    gap:10px;
}

.board{
    padding: 10px;
    background-color: #efefef;
    border-radius: 4px;
}

.items{
    display: flex;
    flex-direction: column;
    gap:5px;

}
.item{
    background-color: white;
    padding:10px;
    
}
</style>