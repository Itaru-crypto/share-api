<template>
  <div>
    <div v-for="(value, key, index) in shares" :key="index">
      <div class="message">
        <div class="flex">
          <p class="name">{{value.name}}</p>
          <img class="icon" src="../assets/heart.png" @click="fav(key)" alt/>
          <p class="number">{{value.like.length}}</p>
          <img 
            class="icon" 
            src="../assets/cross.png" 
            @click="del(key)" 
            alt 
            v-if="path && profile"
          />
          <img 
            class="icon detail" 
            src="../assets/detail.png" 
            @click="
              $router.push({
                path: '/detail/' + value.item.id,
                params: {id: value.item.id}
             })
             "
             alt
             v-if="profile"
          />
        </div>
        <p class="text">{{value.item.share}}</p>
      </div>
    </div>
  </div>
</template>
<script>
import axios from "axios";
export default {
  props: ["id"],
  data(){
    return{
      shares:[],
      path: true,
      profile: true,
    };
  },
  methods: {
    fav(index){
      let result = this.shares[index].like.some((value)=>{
        return value.user_id == this.$store.state.user.id;
      });
      if (result){
        this.shares[index].like.forEach((element) => {
          if(element.user_id == this.$store.state.user.id){
            axios
              .delete("herokuのURL/api/like",{
                data:{
                  share_id: this.shares[index].item.id,
                  user_id: this.$store.state.user.id,
                },
              })
              .then((response) => {
                console.log(response);
                this.$reouter.go({
                  path: this.$router.currentRoute.path,
                  force: true,
                });
              });
          }
        });
      } else {
        axios
          .post("herokuのURL/api/like",{
            share_id: this.shares[index].item.id,
            user_id: this.$store.state.user.id,
          })
          .then((response) => {
            console.log(response);
            this.$router.go({
              path: this.$router.currentRoute.path,
              force: true,
            });
          });
      }
    }
  }
}
</script>

<style scoped>
.flex {
  display: flex;
}
.icon {
  width: 25px;
  height: 25px;
}
.detail {
  margin-left: 50px;
}
.message {
  padding: 20px;
  border-bottom: solid 1px white;
  border-left: solid 1px white;
}
.name {
  font-size: 18px;
  font-weight: bold;
  margin-right: 10px;
}
.text {
  margin-top: 10px;
}
.number {
  margin-left: 10px;
  margin-right: 10px;
}
</style>