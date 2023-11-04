<script lang="ts">
import { defineComponent, onMounted, reactive, ref } from "vue";
import liff from "@line/liff";

type LiffState = {
  profile?: {
    userId: string;
    displayName: string;
    pictureUrl?: string;
    statusMessage?: string;
  };
};

export default defineComponent({
  setup() {
    const liffState = reactive<LiffState>({
      profile: undefined
    });
    const isFriend = reactive({
        flag: false
    });

    const getProfile = async () => {
      const profile = await liff.getProfile();
      liffState.profile = profile;
    };

    onMounted(async () => {
      // LIFFアプリの初期化
      await liff.init({ liffId: '2001486496-dQ3aQLV6' });
      getProfile();
      liff.getFriendship().then((data) => {
        if (data.friendFlag) {
          console.log('ともだち！');
          isFriend.flag = true;
        } else {
          console.log('ともだちじゃない');
        }
      });
    });

    return {
      liffState,
      isFriend,
    };
  }
});
</script>

<template>
    <div>
        <h1>{{ liffState.profile?.displayName ?? 'Unknown' }} さん！</h1>
        <p>
            今日の調子は {{ liffState.profile?.statusMessage ?? 'Unknown' }} だね。<br/>
            あなたは、{{ isFriend.flag ? 'もう友だち！' : 'まだ友だちじゃない！' }}

            {{ liffState.profile?.userId }}
        </p>
        <h1>友だち登録はこちらから</h1>
        <ul>
            <a href="https://lin.ee/rbZnolD">
                <li>link</li>
            </a>
        </ul>

        <h2>hoge</h2>
    </div>
</template>