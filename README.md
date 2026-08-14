# auto_redirect_profile_all

Twitter(自称X)のブラウザ版がアプリ版のようにRT(RP)を除く本人の投稿のみがデフォルトになりました。

RT(RP) した内容を含めて自分のタイムラインを表示するには「ポスト」→「すべて」に変更が必要に。

<img width="547" height="161" alt="profile_new_list" src="https://github.com/user-attachments/assets/5fdf4fd1-5d97-4260-8eda-84f83647a2e6" />

どうしてデフォルトがこっちなのか

　

おそらくPCユーザなら誰しも行ってるであろう「プロフィール」からの動線の場合に

自動的に「すべて」のプロフィールページを開くようにできるスクリプトを作成しました。Gemini 様が。

<img width="297" height="386" alt="26814-171622" src="https://github.com/user-attachments/assets/1f6cc0d9-10ad-4e55-a037-ca41ec2571f9" />

左メニューの「プロフィール」

　

`MY_USERNAME` の部分を自分のアカウントIDに置き換えて利用してください。

なおプルダウンで意図的に「ポスト」を選んだ場合や<br>
ブックマークに保存済の元々の自分のURLリンクなどは `/all` を勝手に付与するようなことはしないようなロジックにしています。


```JavaScript

// ==UserScript==
// @name         X (Twitter) Profile Auto Redirect /all
// @namespace    http://tampermonkey.net
// @version      1.7
// @description  Auto redirect /all page, when you clicked Profile link.
// @match        https://x.com/*
// @match        https://x.com/*/*
// @match        https://twitter.com/*
// @match        https://twitter.com/*/*
// @run-at       document-start
// @grant        none
// ==/UserScript==

(function() {
    'use strict';

    // あなたのユーザー名（@を除いた半角英数）に書き換えてください
    const MY_USERNAME = 'MY_USERNAME';

    const targetPath = `/${MY_USERNAME}/all`;
    const targetUrl = `https://x.com${targetPath}`;

    // 左側メニューの見た目の href 属性を常に固定（中ボタンの新規タブ対策）
    function fixHref(event) {
        const anchor = event.target.closest('a[data-testid="AppTabBar_Profile_Link"]');
        if (anchor && anchor.getAttribute('href') !== targetPath) {
            anchor.setAttribute('href', targetPath);
        }
    }
    document.addEventListener('mouseover', fixHref, true);
    document.addEventListener('mousedown', fixHref, true);

    // 通常の左クリック時の挙動：Xのシステムに処理を渡さず、ブラウザ機能で強制遷移
    document.addEventListener('click', function(event) {
        const anchor = event.target.closest('a[data-testid="AppTabBar_Profile_Link"]');
        if (!anchor) return;

        // 通常の左クリック（ボタン0）かつ、CtrlやCmdキーを押していない場合
        if (event.button === 0 && !event.ctrlKey && !event.metaKey) {
            // X側の遷移処理を完全に殺す（フリーズさせる）
            event.preventDefault();
            event.stopPropagation();
            event.stopImmediatePropagation();

            // 現在いる場所に関わらず、毎回強制的にブラウザレベルで /all に移動
            location.href = targetUrl;
        }
    }, true); // キャプチャリングフェーズ最優先
})();


```

Greasemonkey, Tampermonkey からドウゾ
