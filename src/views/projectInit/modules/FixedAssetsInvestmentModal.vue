<template>
  <j-modal
    :width="1200"
    :visible="visible"
    :maskClosable="false"
    switchFullscreen
    @ok="handleOk"
    @cancel="handleCancel">
     <fixed-assets-investment-form ref="realForm" @ok="submitCallback" @close="handleCancel" :disabled="disableSubmit"/>
 </j-modal>
</template>

<script>

  import FixedAssetsInvestmentForm from './FixedAssetsInvestmentForm'

  export default {
    name: 'FixedAssetsInvestmentModal',
    components: {
      FixedAssetsInvestmentForm
    },
    props:['selectedRows'],
    data() {
      return {
        title:'',
        visible: false,
        disableSubmit: false
      }
    },
    methods:{
      add () {
        this.visible=true
        this.$nextTick(()=>{
          this.$refs.realForm.add();
        })
      },
      edit (record) {
        this.visible=true
        console.log(record,'record2')
        this.$nextTick(()=>{
          console.log(this.$refs.realForm)
          this.$refs.realForm.edit(record);
        })
      },
      close () {
        this.$emit('close');
        this.visible = false;
      },
      handleOk () {
        this.$refs.realForm.handleOk();
      },
      submitCallback(){
        this.$emit('ok');
        this.visible = false;
      },
      handleCancel () {
        this.close()
      }
    }
  }
</script>

<style scoped>
</style>