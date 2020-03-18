<template>
    <div style="text-align:center">
        {{busName}} 정류소에 대한 검색결과는 {{busCount}}건 입니다.
    </div>
</template>
<script>
export default {
    props:[
        'busName',
        'busCount'
    ],
    data(){
        return{

        }
    }
}
</script>