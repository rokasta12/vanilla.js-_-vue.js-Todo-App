<template>
  <r-capsule-field :id="id" :input="input" :radioGroup="radioGroupData" :width="computedWidth" :height="height"
                   :paddingLeft="paddingLeft" :disabled="disabledCompute" :labelFloat="labelFloat"
                   :showLabel="showLabel" :labelVisible="labelVisible" :keyup="keyup" @keydown="keydown"
                   :customLayout="getLayout()" :customStage="getStage()" :initialData="initialData"
                   :validation="validationData">
    <el-select :id="id" v-model="dataValue" :no-data-text="noDataText" :no-match-text="noDataText" :disabled="disabled"
               filterable size="large" v-on:change="updateInput($event)" @change="change" remote
               :remote-method="remoteMethod"
               :clearable="clearable"  
               :placeholder="placeHolder !== undefined && placeHolder !== '' ? getLabel('label_' + placeHolder) : getLabel('label_type_to_search')"
               :value-key="valueAttribute" :class="{validationThenChangeColor: cmpFieldColorChange}" v-validate="validationData"
               :data-vv-as="displayErrorName" :name="computedName" ref="id">

      <div v-if="!customResult">
          <el-option  v-for="item in respData" :style="{'font-size':optionFontSize}"
                 :label="getLabelText(item)" :value="getItem(item)" :key="item[valueAttribute]"></el-option>
    </div
    </el-select>
    <div class="el-form-item__error" style="font-size:10px" v-if="errors.has(computedName)">
      {{resolveError(errors.first(computedName)) }}
    </div>
  </r-capsule-field>
</template>
<script>
  import apicall from './mixins/apicall.js'
  import editField from './mixins/editfield.js'
  import keycode from "./mixins/keycode.js"

  export default {
    name: 'r-search',
    introduction: 'Search component for Rally',
    description: 'Rally Search allows searching through the grid records and filtering content that aggregates all other filter elements.',
    mixins: [apicall, editField, keycode],
    props: {
      valueAttribute: {
        default: 'value',
        type: String,
        note: 'The attribute of the component that is grabbed along with a component if the component is selected by value (see: byValue)'
      },
      labelAttribute: {
        default: 'label',
        type: String,
        note: 'Identifier of the component used during search'
      },
      clearable: {
        default: false,
        type: Boolean,
        note: "Allows selected field to be cleared"
      },
      secondLabelAttribute: {
        default: '',
        type: String,
        note: 'This field is used if a second label wants to be shown as final label in the component'
      },
      labelAttributeInBrace: {
        default: '',
        type: String,
        note: 'Identifier of the item to be shown in brace, if null or empty then nothing will be shown'
      },
      onlyNumber: {
        default: false,
        type: Boolean,
        note : ''
      },
      responseKey: {
        note: 'Specifications for the list of results acquired from search'
      },
      displayErrorName: {
        note: 'Name of the display error on layout if it occurs'
      },
      initialData: {
        note: 'Full list of data being searched'
      },
      url: {
        default: null,
        type: String,
        note: 'Location of the request from backend service'
      },
      selectedChange: {
        type: Function,
        note: 'Function to be called when the selected component is altered'
      },
      type: {
        default: 'text',
        type: String,
        note: 'Type of Search component'
      },
      byValue: {
        default: false,
        type: Boolean,
        note: 'Gets the valueAttribute of the component simultaneously with the component during selection if true, gets all of its attributes otherwise'
      },
      isUpperCase: {
        default: false,
        type: Boolean,
        note: 'Decides if the response text will be shown as upper character or not'
      },
      customResult: {
        default: false,
        type: Boolean,
        note: 'custom result use with other components'
      },
      saveNonQuery: {
        default: false,
        type: Boolean,
        note: ''
      },
      clearInitData: {
        default: false,
        type: Boolean,
        note: 'Clears the initial data and the response data'
      }
    },
    data() {
      return {
        inputText: null,
        isRequest: false,
        isFirstBind: true,
        respData: [],
        query: ''
      }
    },
    watch: {
      initialData: function () {
        if (this.initialData && this.initialData.length > 0) {
          this.respData = this.initialData;
          this.dataValue = this.respData[0]
        }
      },
      clearInitData() {
        this.initialData = this.respData = []
        this.inputText = ''
      }
    },
    computed: {
      noDataText: function () {
        return this.getLabel('label_no_data_text')
      },
      cmpFieldColorChange() {
        const isRequired = this.validationData && this.validationData.includes("required")
        const isEmpty = this.dataValue === null || this.dataValue === undefined || this.dataValue === ""
        return isRequired && isEmpty
      },
    },
    methods: {
      getItem: function (item) {
        if (this.byValue === true) {
          return item[this.valueAttribute]
        } else {
          return item
        }
      },
      keydown: function (evt){
        let charCode = evt.which ? evt.which : evt.keyCode
        if (this.onlyNumber) {
				  if (!this.keycodeCheckForNumberWithArrow(charCode)) {
						  evt.preventDefault()
				  } else if (this.keydown !== null && this.keydown !== undefined) {
					  this.keydown(evt)
				  }
		}
      },.$emit
      getLabelText: function (item) {
        var label = item[this.labelAttribute];
        if (
          this.labelAttributeInBrace !== undefined &&
          this.labelAttributeInBrace != null &&
          this.labelAttributeInBrace !== ''
        ) {
          label = item[this.labelAttributeInBrace] + ' - ' + label
        }
        if (this.secondLabelAttribute && item[this.secondLabelAttribute]) {
          label = label + ' - ' + item[this.secondLabelAttribute]
        }
        return label
      },
      updateInput: function (value) {
        this.$emit('input', value)
      },
      getResponseData: function (query) {
        const vm = this;
        query = encodeURI(query);
        const request = {
          method: 'GET',
          url: vm.url + query,
          timeout: 10000
        };
        vm.respData = [];
        this.rallyQuery(request, response => {
          if (response.hasOwnProperty(vm.responseKey)) {
            const responseList = response[vm.responseKey];
            if (vm.isUpperCase && responseList && responseList.length > 0) {
              responseList.forEach(e => {
                e[vm.labelAttribute] = e[vm.labelAttribute].toString().toUpperCase()
              })
            }

            if(vm.saveNonQuery && Array.isArray(responseList) && responseList.length === 0) {
              responseList.push({id: null, conditionValue: decodeURI(query)})
            }
            vm.respData = responseList;
            vm.$emit('response', vm.respData)
          }
          else{
              vm.respData = response;
              vm.$emit('response', vm.respData);
          }
        })
      },
      remoteMethod: function (query) {
        this.query = query;
        if (query === undefined || query === null || query === '' || query.length < 3) {
          this.respData = [];
        } else {
          if (this.isFirstBind) {
            setTimeout(() => {
              this.isFirstBind = false;
              this.isRequest = true;
              this.remoteMethod(this.query)
            }, 500)
          }
          if (this.isRequest && this.query === query) {
            if (this.inputText === this.query) {
              return
            }
            this.inputText = this.query;
            this.isRequest = false;
            this.getResponseData(this.query);
            this.isFirstBind = true
          }
        }
      }
      // ,
      // change: function (value) {
      //   if (
      //     this.selectedChange !== null &&
      //     this.selectedChange !== undefined &&
      //     this.items !== undefined
      //   ) {
      //     var selectedItem = value
      //     for (var i = 0; i < this.respData.length; i++) {
      //       if (this.respData[i][this.valueAttributeData] === value) {
      //         selectedItem = this.respData[i]
      //       }
      //     }
      //     this.selectedChange(selectedItem)
      //   }
      // }
    }
  }
</script>

