<script lang="ts">
const shuffleMembers = (
  members: Member[],
  pinTheFirstMember = false
): void => {
  let offset = pinTheFirstMember ? 1 : 0
  // `i` is between `1` and `length - offset`
  // `j` is between `0` and `length - offset - 1`
  // `offset + i - 1` is between `offset` and `length - 1`
  // `offset + j` is between `offset` and `length - 1`
  let i = members.length - offset
  while (i > 0) {
    const j = Math.floor(Math.random() * i)
      ;[members[offset + i - 1], members[offset + j]] = [
        members[offset + j],
        members[offset + i - 1]
      ]
    i--
  }
}
</script>

<script setup lang="ts">
import { VTLink } from '@vue/theme'
import membersCoreData from './members-core.json'
import membersEmeritiData from './members-emeriti.json'
import membersPartnerData from './members-partner.json'
import TeamHero from './TeamHero.vue'
import TeamList from './TeamList.vue'
import type { Member } from './Member'
shuffleMembers(membersCoreData as Member[], true)
shuffleMembers(membersEmeritiData as Member[])
shuffleMembers(membersPartnerData as Member[])
</script>

<template>
  <div class="TeamPage">
    <TeamHero>
      <template #title>Meet the Team</template>
      <template #lead>
        Vue এবং এর ইকোসিস্টেম ডেভেলপমেন্ট পরিচালিত হয়
        আন্তর্জাতিক দল দ্বারা, যাদের মধ্যে কেউ কেউ হতে বেছে নিয়েছে
        <span class="nowrap">নীচে বৈশিষ্ট্যযুক্ত।</span>
      </template>

      <template #action>
        <VTLink href="https://github.com/vuejs/governance/blob/master/Team-Charter.md">
          দল সম্পর্কে আরও জানুন
        </VTLink>
      </template>
    </TeamHero>

    <TeamList :members="(membersCoreData as Member[])">
      <template #title>Core Team Members</template>
      <template #lead>
        মূল দলের সদস্য যারা সক্রিয়ভাবে জড়িত
        এক বা একাধিক মূল প্রকল্পের রক্ষণাবেক্ষণ। তারা তৈরি করেছে
        Vue ইকোসিস্টেমে উল্লেখযোগ্য অবদান, দীর্ঘ মেয়াদে
        প্রকল্প এবং এর ব্যবহারকারীদের সাফল্যের প্রতিশ্রুতি।
      </template>
    </TeamList>

    <TeamList :members="(membersEmeritiData as Member[])">
      <template #title>কোর টিম এমেরিটি</template>
      <template #lead>
        এখানে আমরা কিছু আর সক্রিয় নয় এমন মূল দলের সদস্যদের সম্মান করি যারা তৈরি করেছেন
        অতীতে মূল্যবান অবদান।
      </template>
    </TeamList>

    <TeamList :members="(membersPartnerData as Member[])">
      <template #title>সম্প্রদায় অংশীদার</template>
      <template #lead>
        Vue সম্প্রদায়ের কিছু সদস্য এটিকে এতটাই সমৃদ্ধ করেছে যে তারা
        বিশেষ উল্লেখের যোগ্য। আমরা একটি আরো ঘনিষ্ঠ উন্নয়ন করেছি
        এই মূল অংশীদারদের সাথে সম্পর্ক, প্রায়ই তাদের সাথে সমন্বয় করে
        আসন্ন বৈশিষ্ট্য এবং খবর.
      </template>
    </TeamList>
  </div>
</template>

<style scoped>
.TeamPage {
  padding-bottom: 16px;
}

@media (min-width: 768px) {
  .TeamPage {
    padding-bottom: 96px;
  }
}

.TeamList + .TeamList {
  padding-top: 64px;
}
</style>
