[index.html](https://github.com/user-attachments/files/26889293/index.html)[Uploading index.html<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>64 Archetypes</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
    body {
      font-family: system-ui, sans-serif;
      background: linear-gradient(135deg, #f0f4ff 0%, #fff0f5 100%);
      background-image: 
        radial-gradient(circle at 25% 25%, rgba(245, 158, 11, 0.1) 0%, transparent 50%),
        radial-gradient(circle at 75% 75%, rgba(165, 243, 252, 0.15) 0%, transparent 50%),
        repeating-linear-gradient(45deg, transparent, transparent 40px, rgba(251, 191, 36, 0.08) 40px, rgba(251, 191, 36, 0.08) 80px);
    }
  </style>
</head>
<body class="min-h-screen flex items-center justify-center p-6">
  <div class="max-w-xl w-full">

    <!-- スタート画面 -->
    <div id="start" class="text-center">
      <canvas id="startMandala" width="220" height="220" class="mx-auto mb-8"></canvas>
      <h1 class="text-5xl font-light tracking-widest mb-6 text-zinc-800">64 Archetypes</h1>
      <button onclick="startTest()" class="bg-amber-400 hover:bg-amber-300 text-zinc-950 px-14 py-5 rounded-3xl text-2xl font-light shadow-lg">
        診断を始める
      </button>
    </div>

    <!-- 質問画面 -->
    <div id="quiz" class="hidden">
      <div class="mb-8">
        <div class="h-2 bg-white/60 rounded-full overflow-hidden">
          <div id="progress" class="h-2 bg-amber-400 rounded-full w-0 transition-all"></div>
        </div>
        <div class="flex justify-between text-xs text-zinc-500 mt-2">
          <span id="current">1</span>
          <span>100</span>
        </div>
      </div>
      <p id="questionText" class="text-2xl leading-relaxed mb-12 min-h-[130px] text-zinc-800"></p>
      <div class="grid grid-cols-5 gap-3">
        <button onclick="answer(1)" class="py-7 bg-white hover:bg-amber-100 shadow rounded-3xl text-sm text-zinc-800">1<br><span class="text-xs text-zinc-400">全く違う</span></button>
        <button onclick="answer(2)" class="py-7 bg-white hover:bg-amber-100 shadow rounded-3xl text-sm text-zinc-800">2</button>
        <button onclick="answer(3)" class="py-7 bg-white hover:bg-amber-100 shadow rounded-3xl text-sm text-zinc-800">3<br><span class="text-xs text-zinc-400">普通</span></button>
        <button onclick="answer(4)" class="py-7 bg-white hover:bg-amber-100 shadow rounded-3xl text-sm text-zinc-800">4</button>
        <button onclick="answer(5)" class="py-7 bg-white hover:bg-amber-100 shadow rounded-3xl text-sm text-zinc-800">5<br><span class="text-xs text-zinc-400">すごくそう</span></button>
      </div>
    </div>

    <!-- 結果画面（マンダラ削除済み） -->
    <div id="result" class="hidden text-center">
      <h2 class="text-4xl font-light mb-1 text-zinc-700">あなたのタイプ</h2>
      <img id="typeImage" class="w-48 h-48 mx-auto mb-4 rounded-3xl shadow object-cover border-4 border-white" alt="タイプイラスト">
      <div id="typeName" class="text-5xl font-light text-amber-500 mb-3 leading-tight"></div>
      <p id="typeDesc" class="text-zinc-600 text-lg max-w-xs mx-auto"></p>
      <button onclick="shareResult()" class="w-full bg-amber-400 text-zinc-950 py-5 rounded-3xl text-xl mb-3 shadow">Xでシェア</button>
      <button onclick="location.reload()" class="w-full border border-zinc-300 py-5 rounded-3xl text-xl text-zinc-700">もう一度やる</button>
    </div>
  </div>

  <script>
    const questions = [
      "予定を立てて物事を進めるのが好きだ。","友達の気持ちにすぐに気づいて優しくできる。","今の音や匂いや感触を楽しむ。","新しいアイデアが次々浮かんでくる。","一人で静かに考える時間が好きだ。",
      "自分の本当の気持ちを大事にしたい。","昔の思い出を振り返るのが好きだ。","なんとなくピンとくる感覚を信じる。","物事を効率よく片付けるのが好きだ。","みんなを明るくまとめるのが好きだ。",
      "体を動かして遊んだり活動するのが好きだ。","未来の楽しいことを想像するのが好きだ。","問題を論理的に考えて解決するのが好きだ。","自分の気持ちを素直に言葉にする。","細かい感触や匂いに敏感に気づく。",
      "ひらめいたアイデアを追いかけるのが好きだ。","複雑なことをじっくり分析するのが好きだ。","みんなの心が仲良くつながっているのが大事だ。","手を動かして何かを作り上げるのが好きだ。","知らない世界を探検するのが好きだ。",
      "目標を決めてコツコツ頑張る。","みんなの気持ちを繋げてまとめるのが好きだ。","目の前の問題にすぐに立ち向かう。","いろいろな可能性を考えてみるのが好きだ。","深いことを一人でじっくり考える。",
      "自分の心の奥底を大切にする。","過去の経験から学んで活かす。","心の声に耳を澄ます。","ルールや約束をちゃんと守る。","友達が困っているときに支えてあげられる。",
      "今この瞬間を全力で楽しむ。","新しい解決策を考えてみるのが好きだ。","矛盾しているところを見つけて直す。","人のつらさを一緒に感じてあげられる。","五感を使って世界をしっかり味わう。",
      "未来の夢を大きく描くのが好きだ。","知識を整理して体系的にまとめる。","優しい言葉で自分の気持ちを伝える。","体で感じたことを大切にする。","偶然のいい出会いを大切にする。",
      "全体のバランスを考えて整える。","みんなの気持ちをスムーズに調整する。","細かいところによく気づく。","新しいつながりを見つけるのが好きだ。","一人で集中して考える時間が好きだ。",
      "自分の中の大切な価値観を守る。","記憶を頼りに物事を判断する。","不思議なイメージを思い浮かべる。","組織を上手にまとめて動かす。","深い心のつながりを感じる。",
      "体を動かす活動が好きだ。","アイデアを実際に形にする。","論理を武器に挑戦するのが好きだ。","内なる愛情を大切にする。","過去の記憶を活かして判断する。",
      "心の奥にある大切なイメージを守る。","みんなが円くまとまるようにする。","偶然の出会いを喜ぶ。","静かに自分の中心を見つめる。","意味のある偶然を感じ取る。",
      "スケジュールをしっかり管理するのが好きだ。","人の喜ぶ顔を見るのが嬉しい。","触ったり見たりして現実を感じる。","まだ誰も思いつかないことを考える。","静かな場所で頭を整理する。",
      "自分の感情を隠さずに大切にする。","昔の写真や日記を見るのが好きだ。","直感で「これだ！」と思う。","無駄を省いてスッキリ進める。","みんなで協力して楽しくする。",
      "スポーツや外遊びが好きだ。","どんな未来になるか想像するのが楽しい。","理由を考えて答えを出す。","素直に「嬉しい」「悲しい」と言う。","小さな変化にすぐ気づく。",
      "突然のひらめきを大切にする。","難しい問題を分解して考える。","友達同士の仲をよくする。","工作や料理を作るのが好きだ。","新しい場所に行ってみたい。",
      "計画通りに達成するのが嬉しい。","人の話をじっくり聞いてあげる。","今ここにあるものをしっかり感じる。","可能性をたくさん広げる。","一人で考え込む時間が好きだ。",
      "心の奥の気持ちを大切にする。","過去の失敗から学べる。","内なる声に従ってみる。","決まり事を守って安心する。","困っている人を助けたい。",
      "瞬間瞬間を味わう。","普通じゃない解決方法を考える。","矛盾をなくしてスッキリさせる。","友達の悲しみを一緒に感じる。","目や耳や鼻で世界を楽しむ。",
      "夢をどんどん大きくする。","学んだことを整理して覚える。","優しい気持ちを言葉にする。","体が感じたことを信じる。","運命的な出会いを信じる。"
    ];

    const typeNames = [
      "建築家","機械エンジニア","電気エンジニア","プログラマー","経済アナリスト","経営コンサルタント","弁護士","土木技術者",
      "教師","社会福祉士","営業マン","観光ガイド","国際協力員","カウンセラー","イベントプランナー","通訳",
      "看護師","スポーツコーチ","デザイナー","アスリート","料理人","農家","漁師","資源調査員",
      "広告プランナー","イラストレーター","文化交流員","ジャーナリスト","哲学者","人類学者","ライター","小説家",
      "数学者","考古学者","認知科学者","物理学者","化学者","歴史家","言語学者","天文学者",
      "音楽家","美術家","俳優","脚本家","放送作家","保健師","生活相談員","人間関係アドバイザー",
      "民俗研究者","地形調査員","生物研究者","地質調査員","農家","栄養士","環境保護員","商売人",
      "美術史家","映画監督","編集者","文化研究者","思想家","英語教師","行動観察者","発達支援員"
    ];

    const descriptions = [
      "論理的に構造を設計し、周りをまとめ上げる頼れるリーダー",
      "複雑な機械の仕組みを最適化する冷静な技術者",
      "回路のように論理と流れをコントロールするバランスの取れた人",
      "一人で完璧なシステムを構築し続けるクリエイター",
      "市場を論理で分析・征服する鋭い経済アナリスト",
      "組織全体を効率的に導く賢者コンサルタント",
      "タイミングよくルールを適用する客観的な法律家",
      "大規模なインフラを均衡させて完成させる土木のプロ",
      "みんなの気持ちを明るくまとめ、成長を支える教師",
      "人の痛みに寄り添い、社会を優しく支える福祉士",
      "人を説得し、関係を広げていく営業の達人",
      "旅する人たちを楽しく案内する観光ガイド",
      "国境を越えて人々をつなぐ国際協力員",
      "心の奥底まで寄り添うカウンセラー",
      "みんなを巻き込んで楽しいイベントを創るプランナー",
      "言葉の壁を越えて人とつなぐ通訳",
      "命を守り、優しくケアする看護師",
      "体を動かして人を強く導くスポーツコーチ",
      "美しさと機能性を両立させるデザイナー",
      "自分の限界に挑戦し続けるアスリート",
      "五感を活かして美味しいものを作る料理人",
      "大地と自然を育て、命を育てる農家",
      "海の恵みを活かして生きる漁師",
      "資源の可能性を見極める調査員",
      "人の心を動かす広告プランナー",
      "想像力を形にするイラストレーター",
      "文化を越えて人々をつなぐ交流員",
      "真実を追い求め、伝えるジャーナリスト",
      "深い思索で世界を問い続ける哲学者",
      "人間の多様性を探求する人類学者",
      "言葉で世界を紡ぐライター",
      "物語を創り出す小説家",
      "論理の極みを追求する数学者",
      "過去の謎を解き明かす考古学者",
      "人間の心の仕組みを探る認知科学者",
      "宇宙の法則を解き明かす物理学者",
      "物質の秘密を明らかにする化学者",
      "歴史の流れを読み解く歴史家",
      "言葉の起源と構造を研究する言語学者",
      "星や宇宙の神秘を追う天文学者",
      "心を揺さぶる音楽家",
      "美を追求する美術家",
      "感情を体で表現する俳優",
      "物語を紡ぐ脚本家",
      "世の中に情報を届ける放送作家",
      "健康を支える保健師",
      "日常の悩みを解決する生活相談員",
      "人間関係を深く理解するアドバイザー",
      "文化や風習の奥深さを探る民俗研究者",
      "大地の形を読み解く地形調査員",
      "生命の仕組みを研究する生物研究者",
      "地球の歴史を掘り下げる地質調査員",
      "自然と調和して生きる農家",
      "体と食の関係を追求する栄養士",
      "環境を守り、未来を考える保護員",
      "商売を通じて人とつながる商売人",
      "美の歴史を語る美術史家",
      "映像で世界を表現する映画監督",
      "言葉を丁寧に整える編集者",
      "文化の深みを研究する文化研究者",
      "思想の根源を追求する思想家",
      "言語の壁を越えて教える英語教師",
      "人の行動を観察する行動観察者",
      "人の成長を支える発達支援員"
    ];

    const typeImages = [
      "https://picsum.photos/id/1005/400/400","https://picsum.photos/id/1011/400/400","https://picsum.photos/id/102/400/400","https://picsum.photos/id/1033/400/400",
      "https://picsum.photos/id/1040/400/400","https://picsum.photos/id/106/400/400","https://picsum.photos/id/107/400/400","https://picsum.photos/id/1080/400/400",
      "https://picsum.photos/id/109/400/400","https://picsum.photos/id/110/400/400","https://picsum.photos/id/111/400/400","https://picsum.photos/id/112/400/400",
      "https://picsum.photos/id/113/400/400","https://picsum.photos/id/114/400/400","https://picsum.photos/id/115/400/400","https://picsum.photos/id/116/400/400",
      "https://picsum.photos/id/117/400/400","https://picsum.photos/id/118/400/400","https://picsum.photos/id/119/400/400","https://picsum.photos/id/120/400/400",
      "https://picsum.photos/id/121/400/400","https://picsum.photos/id/122/400/400","https://picsum.photos/id/123/400/400","https://picsum.photos/id/124/400/400",
      "https://picsum.photos/id/125/400/400","https://picsum.photos/id/126/400/400","https://picsum.photos/id/127/400/400","https://picsum.photos/id/128/400/400",
      "https://picsum.photos/id/129/400/400","https://picsum.photos/id/130/400/400","https://picsum.photos/id/131/400/400","https://picsum.photos/id/132/400/400",
      "https://picsum.photos/id/133/400/400","https://picsum.photos/id/134/400/400","https://picsum.photos/id/135/400/400","https://picsum.photos/id/136/400/400",
      "https://picsum.photos/id/137/400/400","https://picsum.photos/id/138/400/400","https://picsum.photos/id/139/400/400","https://picsum.photos/id/140/400/400",
      "https://picsum.photos/id/141/400/400","https://picsum.photos/id/142/400/400","https://picsum.photos/id/143/400/400","https://picsum.photos/id/144/400/400",
      "https://picsum.photos/id/145/400/400","https://picsum.photos/id/146/400/400","https://picsum.photos/id/147/400/400","https://picsum.photos/id/148/400/400",
      "https://picsum.photos/id/149/400/400","https://picsum.photos/id/150/400/400","https://picsum.photos/id/151/400/400","https://picsum.photos/id/152/400/400",
      "https://picsum.photos/id/153/400/400","https://picsum.photos/id/154/400/400","https://picsum.photos/id/155/400/400","https://picsum.photos/id/156/400/400",
      "https://picsum.photos/id/157/400/400","https://picsum.photos/id/158/400/400","https://picsum.photos/id/159/400/400","https://picsum.photos/id/160/400/400",
      "https://picsum.photos/id/161/400/400","https://picsum.photos/id/162/400/400","https://picsum.photos/id/163/400/400","https://picsum.photos/id/164/400/400"
    ];

    let current = 0;
    let scores = Array(8).fill(0);

    function startTest() {
      document.getElementById('start').classList.add('hidden');
      document.getElementById('quiz').classList.remove('hidden');
      current = 0;
      scores = Array(8).fill(0);
      showQuestion();
    }

    function showQuestion() {
      if (current >= 100) { finishTest(); return; }
      document.getElementById('questionText').textContent = questions[current];
      document.getElementById('current').textContent = current + 1;
      document.getElementById('progress').style.width = `${(current / 100) * 100}%`;
    }

    function answer(val) {
      scores[current % 8] += val;
      current++;
      showQuestion();
    }

    function finishTest() {
      document.getElementById('quiz').classList.add('hidden');
      document.getElementById('result').classList.remove('hidden');
      const idx = scores.indexOf(Math.max(...scores));
      const typeIndex = idx % 64;
      document.getElementById('typeName').textContent = typeNames[typeIndex];
      document.getElementById('typeDesc').textContent = descriptions[typeIndex];
      document.getElementById('typeImage').src = typeImages[typeIndex];
    }

    function shareResult() {
      const text = `64 Archetypesで「${document.getElementById('typeName').textContent}」になりました！`;
      navigator.clipboard.writeText(text).then(() => alert('コピーしました！ Xに貼ってね'));
    }

    window.onload = function() {
      const canvas = document.getElementById('startMandala');
      const ctx = canvas.getContext('2d');
      ctx.strokeStyle = '#f59e0b';
      ctx.lineWidth = 7;
      for (let i = 0; i < 7; i++) {
        ctx.beginPath();
        ctx.arc(110, 110, 85 - i*10, 0, Math.PI*2);
        ctx.stroke();
      }
      ctx.beginPath();
      ctx.arc(110, 110, 20, 0, Math.PI*2);
      ctx.stroke();
    };
  </script>
</body>
</html>…]()
