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
    /** State declarations */

    const liffState = reactive<LiffState>({
      profile: undefined
    });

    const isFriend = reactive({
        flag: false
    });

    /** Function declarations */

    /**
     * Get user's profile from liff and update this own state.
     */
    const getProfileState = async () => {
      const profile     = await liff.getProfile();
      liffState.profile = profile;
    };

    /**
     * Get URL for friend registration with user and official account.
     * 
     * @param liffId LIFF ID
     * @return URL for friend registration (eg: https://lin.ee/hogehoge)
     */
    const getFriendRegisterUrl = async (liffId: string) => {
        console.log(liffId);

        // TODO: LIFF ID から LINE 公式アカウント追加 URL を算出する API を叩く

        // まだ API がないので固定の URL を返却
        return 'https://lin.ee/rbZnolD';
    };

    /**
     * Write data to a table for inflow route analysis 
     */
    const writeToInflowRouteDB = () => {
        // Do something
    }

    onMounted(async () => {
      // LIFFアプリの初期化
      await liff.init({ liffId: '2001486496-dQ3aQLV6' }); // TODO: 固定値になってるけど、公式アカウントごとに変える必要がある。どうする？
      getProfileState();

      liff.getFriendship().then((data) => {
        isFriend.flag = data.friendFlag;
      });

      if (!isFriend.flag) {
        const liffId: string    = liff.id ?? '';
        const friendRegisterUrl = await getFriendRegisterUrl(liffId);

        // 流入経路分析用データベースに書き込み
        writeToInflowRouteDB();

        window.location.href = friendRegisterUrl;
      }
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
    </div>
</template>