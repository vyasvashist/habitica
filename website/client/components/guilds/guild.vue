<template lang="pug">
// TODO this is necessary until we have a way to wait for data to be loaded from the server
.row(v-if="guild")
  .clearfix.col-8
    .row
      .col-6
        .float-left
          h2 {{guild.name}}
          strong.float-left(v-once) {{$t('groupLeader')}}
          span.float-left : {{guild.leader.profile.name}}
      .col-6
        .float-right
          .row.icon-row
            .col-6
              .svg-icon.shield(v-html="icons.goldGuildBadge")
              span.number {{guild.memberCount}}
              div(v-once) {{ $t('guildMembers') }}
            .col-6
              .item-with-icon
                .svg-icon.gem(v-html="icons.gem")
                span.number {{guild.memberCount}}
                div(v-once) {{ $t('guildBank') }}
    .row.chat-row
      .col-12
        h3(v-once) {{ $t('chat') }}

        textarea(:placeholder="$t('chatPlaceHolder')")
        button.btn.btn-secondary.send-chat.float-right(v-once) {{ $t('send') }}

        .hr
          .hr-middle(v-once) {{ $t('today') }}

        .row
          .col-md-2
            .svg-icon(v-html="icons.like")
          .col-md-10
            .card(v-for="msg in guild.chat", :key="msg.id")
              .card-block
                h3.leader Character name
                span 2 hours ago
                .clearfix
                  strong.float-left {{msg.user}}
                  .float-right {{msg.timestamp}}
                .text {{msg.text}}
                hr
                span.action(v-once)
                  .svg-icon(v-html="icons.like")
                  | {{$t('like')}}
                span.action(v-once)
                  .svg-icon(v-html="icons.copy")
                  | {{$t('copyAsTodo')}}
                span.action(v-once)
                  .svg-icon(v-html="icons.report")
                  | {{$t('report')}}
                span.action(v-once)
                  .svg-icon(v-html="icons.delete")
                  | {{$t('delete')}}
                span.action.float-right
                  .svg-icon(v-html="icons.liked")
                  | +3

  .col-md-4.sidebar
    .guild-background.row
      .col-6
        p Image here
      .col-6
        members-modal(:group='guild')
        br
        button.btn.btn-primary(:class="[isMember ? 'btn-danger' : 'btn-success']") {{ isMember ? $t('leave') : $t('join') }}
        br
        button.btn.btn-primary(v-once) {{$t('inviteToGuild')}}
        br
        button.btn.btn-primary(v-once) {{$t('messageGuildLeader')}}
        br
        button.btn.btn-primary(v-once) {{$t('donateGems')}}
        br
        button.btn.btn-primary(b-btn, @click="updateGuild", v-once) {{ $t('updateGuild') }}
    div
      h3(v-once) {{ $t('description') }}
      p(v-once) {{ guild.description }}
      p Life hacks are tricks, shortcuts, or methods that help increase productivity, efficiency, health, and so on. Generally, they get you to a better state of life. Life hacking is the process of utilizing and implementing these secrets. And, in this guild, we want to help everyone discover these improved ways of doing things.
    div
      h3(v-once) {{$t('guildInformation')}}
      h4 Welcome
      p Below are some resources that some members might find useful. Consider checking them out before posting any questions, as they just might help answer some of them! Feel free to share your life hacks in the guild chat, or ask any questions that you might have. Please peruse at your leisure, and remember: this guild is meant to help guide you in the right direction. Only you will know what works best for you.
    div
      h3 Challenges
      .card
        h4 Challenge
        .row
          .col-8
            p Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla scelerisque ultrices libero.
          .col-4
        .row
          .col-md-12
            span Tag
            span 100
</template>

<style lang="scss" scoped>
@import '~client/assets/scss/colors.scss';

.sidebar {
  background-color: $gray-600;
}

.card {
  margin: 2em 0;
  padding: 1em;

  h3.leader {
    color: $purple-200;
  }

  .text {
    font-size: 16px;
    line-height: 1.43;
    color: $gray-50;
  }
}

.guild-background {
  background-image: linear-gradient(to bottom, rgba($gray-600, 0), $gray-600);
  height: 300px;
}

textarea {
  height: 150px;
  width: 100%;
  background-color: $white;
  border: solid 1px $gray-400;
  font-size: 16px;
  font-style: italic;
  line-height: 1.43;
  color: $gray-300;
  padding: .5em;
}

.svg-icon.shield, .svg-icon.gem {
  width: 40px;
  margin-right: 1em;
}

.icon-row {
  width: 200px;
  margin-top: 3em;
  margin-right: 3em;

  .number {
    font-size: 22px;
    font-weight: bold;
  }
}

.chat-row {
  margin-top: 2em;

  .send-chat {
    margin-top: -3.5em;
    z-index: 10;
    position: relative;
    margin-right: 1em;
  }
}

.hr {
  width: 100%;
  height: 20px;
  border-bottom: 1px solid $gray-500;
  text-align: center;
  margin: 2em 0;
}

.hr-middle {
  font-size: 16px;
  font-weight: bold;
  font-family: 'Roboto Condensed';
  line-height: 1.5;
  text-align: center;
  color: $gray-200;
  background-color: $gray-700;
  padding: .2em;
  margin-top: .2em;
  display: inline-block;
  width: 100px;
}

span.action {
  font-size: 14px;
  line-height: 1.33;
  color: $gray-200;
  font-weight: 500;
  margin-right: 1em;
}

span.action .icon {
  margin-right: .3em;
}
</style>

<script>
import groupUtilities from 'client/mixins/groupsUtilities';
import { mapState } from 'client/libs/store';
import membersModal from './membersModal';

import deleteIcon from 'assets/svg/delete.svg';
import copyIcon from 'assets/svg/copy.svg';
import likeIcon from 'assets/svg/like.svg';
import likedIcon from 'assets/svg/liked.svg';
import reportIcon from 'assets/svg/report.svg';
import gemIcon from 'assets/svg/gem.svg';
import goldGuildBadgeIcon from 'assets/svg/gold-guild-badge.svg';

export default {
  mixins: [groupUtilities],
  props: ['guildId'],
  components: {
    membersModal,
  },
  data () {
    return {
      guild: null,
      icons: Object.freeze({
        like: likeIcon,
        copy: copyIcon,
        report: reportIcon,
        delete: deleteIcon,
        gem: gemIcon,
        goldGuildBadge: goldGuildBadgeIcon,
        liked: likedIcon,
      }),
    };
  },
  computed: {
    ...mapState({user: 'user.data'}),
    isMember () {
      return this.isMemberOfGroup(this.user, this.guild);
    },
    isMemberOfPendingQuest () {
      let userid = this.user._id;
      let group = this.guild;
      if (!group.quest || !group.quest.members) return false;
      if (group.quest.active) return false; // quest is started, not pending
      return userid in group.quest.members && group.quest.members[userid] !== false;
    },
    isMemberOfRunningQuest () {
      let userid = this.user._id;
      let group = this.guild;
      if (!group.quest || !group.quest.members) return false;
      if (!group.quest.active) return false; // quest is pending, not started
      return group.quest.members[userid];
    },
    memberProfileName (memberId) {
      let foundMember = find(this.group.members, function findMember (member) {
        return member._id === memberId;
      });
      return foundMember.profile.name;
    },
    isManager (memberId, group) {
      return Boolean(group.managers[memberId]);
    },
    userCanApprove (userId, group) {
      if (!group) return false;
      let leader = group.leader._id === userId;
      let userIsManager = Boolean(group.managers[userId]);
      return leader || userIsManager;
    },
  },
  created () {
    this.fetchGuild();
  },
  watch: {
    // call again the method if the route changes (when this route is already active)
    $route: 'fetchGuild',
  },
  methods: {
    updateGuild () {
      this.$store.state.editingGroup = this.guild;
      this.$root.$emit('show::modal', 'guild-form');
    },
    async fetchGuild () {
      this.guild = await this.$store.dispatch('guilds:getGroup', {groupId: this.guildId});

      this.guild.chat = [
        {
          text: '@CharacterName Vestibulum ultricies, lorem non bibendum consequat, nisl lacus semper nulla, hendrerit dignissim ipsum erat eu odio. Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla at aliquet urna. Vestibulum ante ipsum primis in faucibus orci luctus et ultrices posuere cubilia Curae; Nulla non est ut nisl interdum tincidunt in eu dui. Proin condimentum a.',
        },
      ];
    },
    deleteAllMessages () {
      if (confirm(this.$t('confirmDeleteAllMessages'))) {
        // User.clearPMs();
      }
    },
    // inviteOrStartParty (group) {
      // Analytics.track({'hitType':'event','eventCategory':'button','eventAction':'click','eventLabel':'Invite Friends'});

      // var sendInviteText = window.env.t('sendInvitations');
      // if (group.type !== 'party' && group.type !== 'guild') {
      //   $location.path("/options/groups/party");
      //   return console.log('Invalid group type.')
      // }
      //
      // if (group.purchased && group.purchased.plan && group.purchased.plan.customerId) sendInviteText += window.env.t('groupAdditionalUserCost');
      //
      // group.sendInviteText = sendInviteText;
      //
      // $rootScope.openModal('invite-' + group.type, {
      //   controller:'InviteToGroupCtrl',
      //   resolve: {
      //     injectedGroup: function() {
      //       return group;
      //     },
      //   },
      // });
    // },
  },
};
</script>
