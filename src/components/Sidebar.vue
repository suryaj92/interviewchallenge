<template>
  <div class="sidebar">
    <el-drawer title="Create an invoice" :visible.sync="drawer">
      <el-divider class="sidebar__divider"></el-divider>
      <el-row class="sidebar__recents">
        <el-col :span="24"
          ><h4>
            Who are you sending the invoice to?
          </h4>
          <h4>
            Recents
          </h4>
          <horizontal-scroll
            class="horizontal-scroll sidebar__recents__contacts"
          >
            <div class="sidebar__recents__contacts__outer">
              <div
                v-for="item in recentContacts"
                :key="item.firstname"
                class="sidebar__recents__contacts__inner_content"
              >
                <span :style="{ background: item.color }">
                  {{ item.firstname[0] }}
                </span>
                <p>
                  {{
                    item.firstname.length > 7
                      ? item.firstname.slice(0, 7) + "..."
                      : item.firstname
                  }}
                </p>
              </div>
            </div>
          </horizontal-scroll>
          <el-divider></el-divider>
        </el-col>
      </el-row>
      <div class="sidebar__contact_form">
        <el-form :model="ruleForm" status-icon :rules="rules" ref="ruleForm">
          <el-form-item label="Name" prop="name">
            <el-button
              type="text"
              icon="el-icon-plus"
              style="float:right"
              @click="contactlists = true"
              >Select from contacts</el-button
            >
            <el-input
              type="text"
              v-model="ruleForm.name"
              autocomplete="off"
            ></el-input>
          </el-form-item>
          <el-form-item label="Email" prop="email">
            <el-input
              type="email"
              v-model="ruleForm.email"
              autocomplete="off"
            ></el-input>
          </el-form-item>
        </el-form>
        <el-button type="text" icon="el-icon-plus">Add another email</el-button>
        <el-row
          type="flex"
          justify="space-between"
          class="sidebar__contact_form__footer"
        >
          <el-col
            ><el-button type="text" @click="drawer = false"
              >Cancel</el-button
            ></el-col
          >
          <el-col
            ><el-button
              type="primary"
              @click="submitForm('ruleForm')"
              style="float:right"
              >Next</el-button
            ></el-col
          >
        </el-row>
      </div>
    </el-drawer>
    <el-drawer :visible.sync="contactlists" :withHeader="false">
      <div class="sidebar__selectcontact">
        <h4>
          <span @click="contactlists = false"><i class="el-icon-back"/></span>
          Select Contact
        </h4>
        <el-divider></el-divider>
        <el-input
          prefix-icon="el-icon-search"
          placeholder="Search contacts..."
          clearable
        >
        </el-input>
        <div class="sidebar__selectcontact__scroll_list">
          <ul>
            <li v-for="item in allContacts" :key="item.firstname">
              <h4 v-if="typeof item === 'string'" :id="item">
                {{ item }}
              </h4>
              <span v-else>
                {{ item.firstname }}<br />
                {{ item.email }}
                <el-divider></el-divider>
              </span>
            </li>
          </ul>
          <div class="sidebar__selectcontact__alphabet">
            <div v-for="item in allContacts" :key="item.firstname">
              <h4 v-if="typeof item === 'string'">
                {{ item }}
              </h4>
            </div>
          </div>
        </div>
      </div>
    </el-drawer>
  </div>
</template>

<script>
import _ from "lodash";
import contacts from "../data/contacts.js";
import HorizontalScroll from "vue-horizontal-scroll";
import "vue-horizontal-scroll/dist/vue-horizontal-scroll.css";

const contactItems = _.flow([
  (arr) => _.orderBy(arr, "firstname"),
  (arr) => _.groupBy(arr, (o) => _.get(o, "firstname[0]", "").toUpperCase()),
  (groups) => _.flatMap(groups, (v, k) => [k, ...v]),
]);

export default {
  name: "Sidebar",
  components: {
    HorizontalScroll,
  },
  data() {
    return {
      drawer: true,
      contactlists: false,
      labelPosition: "top",
      ruleForm: {
        name: "",
        email: "",
      },
      rules: {
        name: [
          {
            required: true,
            message: "Name is required",
            trigger: "blur",
          },
        ],
        email: [
          {
            required: true,
            message: "Email is required",
            trigger: "blur",
          },
        ],
      },
      allContacts: contactItems(contacts),
      recentContacts: contacts,
    };
  },
  methods: {
    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          alert("submit!");
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },
  },
};
</script>

<style lang="scss">
@import "../assets/scss/_variables";

.sidebar {
  .el-input__inner:focus {
    border-color: $primary-color;
  }
  .el-drawer__header {
    color: $black;
    margin-bottom: 0;
    font-weight: 600;
  }
  &__divider {
    margin: 24px 0 0;
  }
  &__recents {
    padding: 0 20px 20px;
    .el-divider--horizontal {
      margin: 6px 0 0;
    }
    h4 {
      font-weight: 600;
    }
    &__contacts {
      display: flex;
      width: 100%;
      height: 125px;
      span {
        color: $white;
        padding: 15px;
        border-radius: 50%;
        display: block;
        width: 15px;
        height: 15px;
        text-align: center;
        margin: 0 auto;
      }
      &__outer {
        display: flex;
        flex: 1;
        width: auto;
        flex-flow: row nowrap;
        align-items: center;
      }
      &__inner_content {
        flex-shrink: 0;
        align-items: center;
        justify-content: center;
        width: 75px;
        text-align: center;
        border-radius: 5px;
      }
      &__inner_content:not(:first-of-type) {
        margin-left: 30px;
      }
    }
  }
  &__contact_form {
    padding: 0 20px 20px;
    .el-form-item__label {
      padding: 0;
      float: left;
      font-weight: 600;
    }
    .el-form-item.last-item {
      margin-bottom: 0;
    }
    .el-button--text {
      color: $primary-color;
    }
    .el-button--primary {
      color: $white;
      background: $primary-color;
      border-color: $primary-color;
    }
    &__footer {
      margin-top: 100px;
    }
  }
  &__selectcontact {
    padding: 0 20px 20px;
    h4 {
      font-weight: 600;
      span {
        cursor: pointer;
        .el-icon-back:before {
          font-size: 20px;
        }
      }
    }
    &__scroll_list {
      .el-divider--horizontal {
        margin: 12px 0;
      }
      overflow: auto;
      height: 80vh;
      width: 90%;
      padding: 0;
      margin: 0;
      ul {
        list-style: none;
        margin-block-start: 10px;
        padding-inline-start: 10px;
      }
    }
    &__alphabet {
      position: fixed;
      top: 130px;
      right: 30px;
      h4 {
        margin: 10px 0;
      }
    }
  }
}
</style>
