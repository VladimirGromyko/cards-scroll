<template>
<div class="card-wrapper">
  <img :src="item.picture.thumbnail" class="mini-photo">
  <div class="card-info">
    <div>
      {{ `name: ${item.name.title} ${item.name.first} ${item.name.last}` }}
    </div>
    <div>
      {{ `email: ${item.email}` }}
    </div>
  </div>

</div>


</template>
<script>
export default {
  props: ["item"],
  name: 'Card',
}

</script>
<style scoped>
.mini-photo{
  padding: 5px;
  width: 50px;
  height: 50px;
  border-radius: 50%;
}
.card-wrapper{
  display: flex;
}
.card-info{
  display: flex;
  flex-direction: column;
  padding: 5px 10px;
}
</style>
