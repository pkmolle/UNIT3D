<template>
    <div class="messages">
        <ul class="list-group">
            <li class="sent"
                v-for="pm in pms">

                <h4 class="list-group-item-heading">

                    <span class="badge-user text-bold">

                        <i v-tooltip="pm.user.group.name"
                           :class="pm.user.group.icon">

                        </i>

                        <a :style="userStyles(pm.user)">
					        {{ pm.user.username }}
                        </a>

                        <!--<i v-if="canMod(message)"-->
                        <!--v-tooltip="`Edit Message`"-->
                        <!--@click="editMessage(message)"-->
                        <!--class="fa fa-edit text-blue">-->

                        <!--</i>-->

                        <i v-if="canMod(pm)"
                           v-tooltip="`Delete Message`"
                           @click="deleteMessage(pm.id)"
                           class="fa fa-times text-red">

                        </i>

					</span>

                    <span class="text-muted">
                        {{ pm.created_at | fromNow }}
                    </span>

                </h4>

                <div :class="['pm-container', pm.user.id === 1 ? 'system' : null]"
                     v-html="pm.message">

                </div>

            </li>
        </ul>
    </div>
</template>
<script>
  import moment from 'moment'

  export default {
    props: {
      pms: {required: true},
    },
    data () {
      return {
        editor: null
      }
    },
    methods: {
      canMod (pm) {
        /*
            A user can Mod his own messages
        */

        return (pm.user.id === this.$parent.auth.id)
      },
      editMessage (pm) {

      },
      deleteMessage (id) {
        axios.get(`/api/chat/message/${id}/delete`)
      },
      userStyles (user) {
        return `cursor: pointer; color: ${user.group.color}; background-image: ${user.group.effect};`
      }
    },
    filters: {
      fromNow (dt) {
        return moment(String(dt)).fromNow()
      }
    },
    created () {
      this.interval = setInterval(() => this.$forceUpdate(), 30000)
    },
    beforeDestroy () {
      clearInterval(this.interval)
    }
  }
</script>