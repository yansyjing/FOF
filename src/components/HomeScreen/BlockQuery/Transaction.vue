<template>
  <div class="tranc">

    <div class="search-box">
      <label>
        <input class="search-input base-shadow" v-model="searchVal" :placeholder="$t('tradeHashOrAddress')">
      </label>
      <icon class="search-icon" type="search" @click.native="onSearch"></icon>
    </div>

    <div class="search-list" v-show="!isDetail && list.length">
      <div class="search-content" @scroll="onContentScroll">
        <div class="list" v-for="(item, index) in list" :key="index"
             @click="showDetail(item)">
          <flexbox>
            <flexbox-item :span="3">
              <div class="tc blue-text">{{ $t('transactionAmount') }}</div>
            </flexbox-item>
            <flexbox-item class="ml-40">
              <div class="ml-20">
                <span class="green-text">{{ item.txValue }}</span>
                <span class="fr text">{{ item.time }}</span>
              </div>
            </flexbox-item>
          </flexbox>
          <!--<flexbox class="mt-20" style="align-items: flex-start">-->
          <!--<flexbox-item :span="3"><div class="title fz-38">{{ $t('transactionHash') }}</div></flexbox-item>-->
          <!--<flexbox-item><div class="text-c1 fz-38">{{ item.txHash }}</div></flexbox-item>-->
          <!--</flexbox>-->
          <flexbox class="mt-20" style="align-items: flex-start">
            <flexbox-item :span="3">
              <div class="title fz-38">{{ $t('rollOutSide') }}</div>
            </flexbox-item>
            <flexbox-item>
              <div class="text ml-20">{{ item.txFrom }}</div>
            </flexbox-item>
          </flexbox>
          <flexbox class="mt-20" style="align-items: flex-start">
            <flexbox-item :span="3">
              <div class="title fz-38">{{ $t('theTransferee') }}</div>
            </flexbox-item>
            <flexbox-item>
              <div class="text ml-20">{{ item.txTo }}</div>
            </flexbox-item>
          </flexbox>

        </div>
      </div>
    </div>

    <div class="no-list" v-show="!list.length">
      <span></span>
      <p>{{ $t('noData') }}</p>
    </div>

    <div class="detail-list" v-show="isDetail">
      <div class="detail-content">
        <div class="list" v-for="(item, index) in detailList" :key="index">
          <div class="head">{{ index }}</div>
          <div class="body">{{ item }}</div>
        </div>
      </div>

      <div class="pd-40">
        <x-button type="primary" @click.native="isDetail = false">{{ $t('backToList') }}</x-button>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    name: "Transaction",
    data() {
      return {
        searchVal: '',
        list: [],
        pageNum: 1,
        pageSize: 20,
        addr: null,
        hash: null,
        noMoreFlag: false,
        isDetail: false,
        detailList: {},
      }
    },
    methods: {
      onSearch() {
        if (!this.searchVal) {
          this.addr = null
          this.hash = null
          this.noMoreFlag = false
          this.pageNum = 1
          this.list = []
          this.getData()
          return
        }

        if (this.$web3.utils.isAddress(this.searchVal)) {
          this.addr = this.searchVal
          this.hash = null
          this.noMoreFlag = true
          this.pageNum = 1
          this.list = []
          this.getData()
        } else if (this.searchVal.length === 66) {
          this.addr = null
          this.hash = this.searchVal
          this.noMoreFlag = true
          this.pageNum = 1
          this.list = []
          this.getData()
        } else {
          this.$vux.toast.text(this.$t('invalidHashOrAddress'))
        }
      },
      onContentScroll(e) {
        let isBottom = this.$funs.isScrollBottom(e)
        if (isBottom && !this.noMoreFlag) {
          this.pageNum++
          this.getData()
        }
      },
      getData() {
        this.$axios.getTradeRecord(this.addr, this.hash, this.pageSize, this.pageNum).then(data => {
          for (let i = 0; i < data.length; i++) {
            data[i].txValue = Math.round(data[i].txValue / 10 ** 16) / 100 + ' FOF'
            this.list.push(data[i])
          }
          if (data.length < this.pageSize) {
            this.noMoreFlag = true
          }
        })
      },
      showDetail(item) {
        this.$web3.eth.getTransaction(item.txHash, (err, data) => {
          if (data) {
            delete data.transactionIndex
            delete data.datasourcecode
            this.detailList = data
            this.isDetail = true
          }
          if (err)
            console.log(err);
        })
      }
    },
    created() {
      this.getData()
    }
  }
</script>

<style lang="less" scoped>
  .tranc {
    position: relative;

    .search-box {
      position: absolute;
      top: -420px;
      width: 100%;
      padding: 0 40px;
      box-sizing: border-box;
      .search-icon {
        right: 65px;
      }
    }

    .search-list {
      height: 100%;
      .search-content {
        height: 100%;
        overflow-y: auto;
        padding: 40px;
        padding-top: 0;
        box-sizing: border-box;

        .list {
          padding: 40px;
          box-sizing: border-box;
          margin-top: 40px;
          border:2px solid #4389F5;
          border-radius: 32px;

          .blue-text {
            color: #4769F5;
          }
          .green-text {
            color: #5BB473;
          }

          .title {
            padding: 5px 0;
            background: #F1F7FF;
            border-radius: 10px;
            color:  @text-color-1;
            text-align: center;
            font-size: 38px;
          }
          .text {
            color:  @text-color-1;
            word-break: break-all;
            font-size: 38px;
          }
        }
      }
    }

    .no-list {
      height: 100%;
      width: 100%;
      span {
        display: inline-block;
        width: 518px;
        height: 436px;
        margin-top: 160px;
        background: url("../../../assets/images/default/empty_data_block_transaction.png") no-repeat;
        background-size: cover;
      }
      p {
        color: #8A9EED;
        font-size: 34px;
        margin-top: 30px;
      }
    }

    .detail-list {
      height: 100%;
      padding: 40px;
      box-sizing: border-box;

      .detail-content {
        height: calc(100% - 170px);
        overflow-y: auto;
        box-sizing: border-box;
        margin-bottom: 0;
        padding: 40px;
        border:2px solid #4389F5;
        border-radius: 32px;

        .list {
          display: flex;
          padding: 20px 0;
          &:not(:first-child) {
            border-top:1px solid #DFEDFF;
          }


          .head {
            width: 300px;
            font-size: 44px;
            word-break: break-word;
            color: #506FF2;
          }
          .body {
            flex: 1;
            padding-left: 40px;
            text-align: left;
            color: @text-color-1;
            font-size: 44px;
            word-break: break-all;
          }
        }
      }
    }
  }
</style>
