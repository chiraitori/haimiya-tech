<script>
  import { onMount } from 'svelte';

  let activeTab = $state('about');
  let bgMode = $state('natural'); // 'natural' | 'cover' | 'desk'
  let clearView = $state(false);
  let osakaTime = $state('');
  let currentMoodIndex = $state(0);

  const moods = [
    {
      place: 'Room 204 — Infirmary Window',
      note: 'Watching cherry petals drift past the second-floor courtyard.',
      drink: 'Iced caramel latte (extra sweet)'
    },
    {
      place: 'After School — Umeda Arcade',
      note: 'Looking at silver ear cuffs I probably do not need.',
      drink: 'Seasonal sakura frappé'
    },
    {
      place: 'Quiet Hallway — West Stairwell',
      note: 'Pretending to be asleep so nobody asks about the committee log.',
      drink: 'Warm milk tea from the vending machine'
    }
  ];

  let personalNotes = $state([
    {
      id: 1,
      date: 'Thu, 16:15',
      tag: 'Health Room',
      text: 'Restocked the adhesive bandages in the second drawer. Left the south window cracked open because the breeze felt nice.'
    },
    {
      id: 2,
      date: 'Tue, 17:40',
      tag: 'Outing',
      text: 'The new seasonal strawberry drink near the station is actually really good. Do not tell anyone I ordered it with extra whipped cream.'
    },
    {
      id: 3,
      date: 'Sun, 14:10',
      tag: 'Personal',
      text: 'Trimmed the side layers of my hair a little too short again. At least the silver clip holds it back.'
    }
  ]);

  let newNoteText = $state('');
  let newNoteTag = $state('Memo');

  let guestMessages = $state([
    {
      name: 'Kurose',
      time: 'Yesterday',
      message: 'Thanks for covering the afternoon infirmary shift and leaving the window key.'
    }
  ]);

  let visitorName = $state('');
  let visitorMessage = $state('');
  let noteConfirmation = $state('');

  const favorites = [
    {
      category: 'Afternoon Habit',
      title: 'The sunlit chair by the infirmary curtain',
      detail: 'Best spot at 4:00 PM when the rest of the hallway quiets down.'
    },
    {
      category: 'Accessories',
      title: 'Brushed silver helix cuffs & small hoops',
      detail: 'Everyone thinks they look intimidating. I just like how they catch the light.'
    },
    {
      category: 'Sweet Tooth',
      title: 'Limited-edition café parfaits & frappés',
      detail: 'People expect me to drink bitter black coffee. I always add two sugars.'
    },
    {
      category: 'Hometown',
      title: 'Kansai cadence when I get flustered',
      detail: 'Comes out automatically whenever someone catches me off guard.'
    }
  ];

  function updateClock() {
    try {
      const formatter = new Intl.DateTimeFormat('en-US', {
        timeZone: 'Asia/Tokyo',
        hour: '2-digit',
        minute: '2-digit',
        hour12: false
      });
      osakaTime = formatter.format(new Date());
    } catch {
      osakaTime = '16:42';
    }
  }

  function cycleMood() {
    currentMoodIndex = (currentMoodIndex + 1) % moods.length;
  }

  function addPersonalNote(e) {
    e.preventDefault();
    const trimmed = newNoteText.trim();
    if (!trimmed) return;
    personalNotes = [
      {
        id: Date.now(),
        date: `Today, ${osakaTime || '16:45'}`,
        tag: newNoteTag || 'Memo',
        text: trimmed
      },
      ...personalNotes.slice(0, 2)
    ];
    newNoteText = '';
  }

  function leaveDeskNote(e) {
    e.preventDefault();
    const msg = visitorMessage.trim();
    if (!msg) return;
    const sender = visitorName.trim() || 'Classmate';
    guestMessages = [
      {
        name: sender,
        time: `Just now (${osakaTime || 'Today'})`,
        message: msg
      },
      ...guestMessages.slice(0, 1)
    ];
    visitorName = '';
    visitorMessage = '';
    noteConfirmation = 'Left on the infirmary desk.';
    setTimeout(() => {
      noteConfirmation = '';
    }, 3000);
  }

  onMount(() => {
    updateClock();
    const timer = setInterval(updateClock, 15000);
    return () => clearInterval(timer);
  });
</script>

<div class="viewport-shell" data-bg-mode={bgMode}>
  <!-- Full-bleed Haimiya Mio background stage -->
  <div class="background-stage" aria-hidden="true">
    <div
      class="bg-ambient"
      style="background-image: url({bgMode === 'desk' ? '/images/mio-desk.jpg' : '/images/mio-spring.jpg'})"
    ></div>

    <div class="bg-character-wrap">
      <img
        class="bg-character-img"
        src={bgMode === 'desk' ? '/images/mio-desk.jpg' : '/images/mio-spring.jpg'}
        alt="Haimiya Mio in soft natural light"
      />
    </div>

    <div class="bg-sunlight-wash"></div>
  </div>

  <!-- Top bar -->
  <header class="top-bar">
    <div class="identity-mark">
      <span class="kanji-name" lang="ja">灰宮 澪</span>
      <span class="divider-line" aria-hidden="true"></span>
      <span class="romaji-name">Mio Haimiya</span>
    </div>

    <div class="top-controls">
      <div class="time-pill" title="Current time in Osaka">
        <span class="sun-dot" aria-hidden="true"></span>
        <span>Osaka {osakaTime}</span>
      </div>

      <div class="bg-switcher" role="group" aria-label="Background framing">
        <button
          type="button"
          class:active={bgMode === 'natural'}
          onclick={() => (bgMode = 'natural')}
        >
          Spring Light
        </button>
        <button
          type="button"
          class:active={bgMode === 'cover'}
          onclick={() => (bgMode = 'cover')}
        >
          Close Frame
        </button>
        <button
          type="button"
          class:active={bgMode === 'desk'}
          onclick={() => (bgMode = 'desk')}
        >
          Desk Portrait
        </button>
      </div>

      <button
        type="button"
        class="clear-toggle"
        class:active={clearView}
        onclick={() => (clearView = !clearView)}
      >
        {clearView ? 'Show Notebook' : 'Admire Background'}
      </button>
    </div>
  </header>

  <!-- Main single-screen workspace -->
  <main class="workspace" class:is-hidden={clearView}>
    <section class="personal-sheet" aria-label="Personal space of Mio Haimiya">
      <!-- Identity & voice intro -->
      <div class="sheet-header">
        <div class="portrait-row">
          <img
            class="mini-avatar"
            src="/images/mio-closeup.png"
            alt="Mio Haimiya close-up portrait"
          />
          <div class="title-group">
            <p class="role-line">Third-Year · Health Committee</p>
            <h1>Mio Haimiya</h1>
          </div>
        </div>

        <p class="personal-quote">
          “People in the hallway always think I’m glaring at them. Honestly, the afternoon sun by the window is just bright—and I’m probably thinking about what sweet drink to get on the way home.”
        </p>
      </div>

      <!-- Navigation tabs (keeps everything in 1 screen without scrolling) -->
      <nav class="sheet-tabs" aria-label="Sections">
        <button
          type="button"
          class:selected={activeTab === 'about'}
          onclick={() => (activeTab = 'about')}
        >
          Profile
        </button>
        <button
          type="button"
          class:selected={activeTab === 'notes'}
          onclick={() => (activeTab = 'notes')}
        >
          Pocket Notes
        </button>
        <button
          type="button"
          class:selected={activeTab === 'likes'}
          onclick={() => (activeTab = 'likes')}
        >
          Small Likes
        </button>
        <button
          type="button"
          class:selected={activeTab === 'desk'}
          onclick={() => (activeTab = 'desk')}
        >
          Infirmary Desk
        </button>
      </nav>

      <!-- Tab Panels -->
      <div class="tab-stage">
        {#if activeTab === 'about'}
          <div class="panel-about">
            <p class="prose-paragraph">
              Born and raised in Kansai. Most days you can find me sitting by the open window in the school infirmary when it is my turn on committee duty. Everyone says the silver piercings and wolf-cut make me look unapproachable, so it is always a little relief when someone just talks to me normally.
            </p>

            <dl class="natural-facts">
              <div class="fact-item">
                <dt>Usual Spot</dt>
                <dd>Second-floor infirmary, window seat</dd>
              </div>
              <div class="fact-item">
                <dt>Speech</dt>
                <dd>Kansai dialect (especially when teased)</dd>
              </div>
              <div class="fact-item">
                <dt>Style</dt>
                <dd>Layered silver-ash hair, helix cuffs, loose tie</dd>
              </div>
              <div class="fact-item">
                <dt>Weakness</dt>
                <dd>Seasonal café menus & unexpected compliments</dd>
              </div>
            </dl>

            <div class="status-box">
              <div class="status-info">
                <span class="status-heading">Right now · {moods[currentMoodIndex].place}</span>
                <p class="status-detail">{moods[currentMoodIndex].note}</p>
                <span class="status-drink">On the desk: {moods[currentMoodIndex].drink}</span>
              </div>
              <button type="button" class="gentle-btn" onclick={cycleMood}>
                Switch moment
              </button>
            </div>
          </div>
        {:else if activeTab === 'notes'}
          <div class="panel-notes">
            <form class="quick-note-form" onsubmit={addPersonalNote}>
              <input
                type="text"
                placeholder="Jot down a quick note for today..."
                bind:value={newNoteText}
                maxlength="110"
                aria-label="New personal note"
              />
              <button type="submit" class="gentle-btn">Pin Note</button>
            </form>

            <ul class="notes-list">
              {#each personalNotes as note (note.id)}
                <li class="note-item">
                  <div class="note-meta">
                    <span class="note-tag">{note.tag}</span>
                    <span class="note-date">{note.date}</span>
                  </div>
                  <p class="note-body">{note.text}</p>
                </li>
              {/each}
            </ul>
          </div>
        {:else if activeTab === 'likes'}
          <div class="panel-likes">
            <div class="likes-grid">
              {#each favorites as item}
                <article class="like-entry">
                  <span class="like-cat">{item.category}</span>
                  <h2>{item.title}</h2>
                  <p>{item.detail}</p>
                </article>
              {/each}
            </div>
          </div>
        {:else if activeTab === 'desk'}
          <div class="panel-desk">
            <p class="desk-intro">
              If I am out getting a drink after committee duty, leave a short memo on the infirmary clipboard.
            </p>

            <form class="desk-form" onsubmit={leaveDeskNote}>
              <div class="form-row">
                <input
                  type="text"
                  placeholder="Your name"
                  bind:value={visitorName}
                  maxlength="32"
                  aria-label="Your name"
                />
                <button type="submit" class="gentle-btn primary">Leave Note</button>
              </div>
              <textarea
                rows="2"
                placeholder="Write a short note for Mio..."
                bind:value={visitorMessage}
                maxlength="140"
                aria-label="Message content"
              ></textarea>
            </form>

            {#if noteConfirmation}
              <p class="confirm-banner" role="status">{noteConfirmation}</p>
            {/if}

            <div class="recent-memos">
              {#each guestMessages as memo}
                <div class="memo-slip">
                  <div class="memo-top">
                    <strong>{memo.name}</strong>
                    <span>{memo.time}</span>
                  </div>
                  <p>{memo.message}</p>
                </div>
              {/each}
            </div>
          </div>
        {/if}
      </div>
    </section>
  </main>

  <!-- Subtle bottom caption -->
  <footer class="bottom-bar">
    <div class="season-caption">
      <span>Spring Breeze · Second Floor Window</span>
    </div>

    {#if clearView}
      <button type="button" class="restore-pill" onclick={() => (clearView = false)}>
        Return to Mio’s Notebook
      </button>
    {/if}
  </footer>
</div>

<style>
  .viewport-shell {
    position: relative;
    width: 100vw;
    min-height: 100dvh;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    padding: 1.5rem 2.25rem;
    overflow: hidden;
    isolation: isolate;
  }

  @media (min-width: 900px) {
    .viewport-shell {
      height: 100dvh;
      max-height: 100dvh;
    }
  }

  /* Background Stage */
  .background-stage {
    position: fixed;
    inset: 0;
    z-index: -1;
    overflow: hidden;
    background-color: #f6eaee;
  }

  .bg-ambient {
    position: absolute;
    inset: -6%;
    background-position: center 22%;
    background-size: cover;
    background-repeat: no-repeat;
    filter: blur(26px) saturate(1.06) brightness(1.03);
    opacity: 0.72;
    transform: scale(1.05);
    transition: background-image 0.5s ease;
  }

  .bg-character-wrap {
    position: absolute;
    inset: 0;
    display: flex;
    justify-content: flex-end;
    align-items: center;
    padding-right: 3vw;
    pointer-events: none;
  }

  .bg-character-img {
    height: 100dvh;
    width: auto;
    max-width: 68vw;
    object-fit: contain;
    object-position: right center;
    mask-image: linear-gradient(to right, transparent 0%, black 14%, black 100%);
    -webkit-mask-image: linear-gradient(to right, transparent 0%, black 14%, black 100%);
    transition: all 0.55s cubic-bezier(0.22, 1, 0.36, 1);
    filter: contrast(1.01);
  }

  /* Close Frame mode: full-bleed cover across entire viewport */
  .viewport-shell[data-bg-mode='cover'] .bg-character-wrap {
    padding-right: 0;
  }

  .viewport-shell[data-bg-mode='cover'] .bg-character-img {
    width: 100vw;
    max-width: 100vw;
    height: 100dvh;
    object-fit: cover;
    object-position: 65% 18%;
    mask-image: none;
    -webkit-mask-image: none;
  }

  .viewport-shell[data-bg-mode='desk'] .bg-character-img {
    height: 96dvh;
    object-position: right bottom;
  }

  .bg-sunlight-wash {
    position: absolute;
    inset: 0;
    background:
      radial-gradient(circle at 82% 16%, rgba(255, 249, 245, 0.35), transparent 45%),
      linear-gradient(
        90deg,
        rgba(251, 246, 245, 0.82) 0%,
        rgba(251, 246, 245, 0.46) 42%,
        rgba(251, 246, 245, 0.04) 74%
      );
    pointer-events: none;
  }

  /* Top Bar */
  .top-bar {
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 1rem;
    z-index: 2;
    flex-shrink: 0;
  }

  .identity-mark {
    display: inline-flex;
    align-items: center;
    gap: 0.75rem;
    padding: 0.45rem 0.95rem;
    background: var(--panel-surface);
    backdrop-filter: blur(14px);
    -webkit-backdrop-filter: blur(14px);
    border: 1px solid rgba(255, 255, 255, 0.7);
    border-radius: 999px;
    box-shadow: 0 4px 20px rgba(35, 33, 38, 0.04);
  }

  .kanji-name {
    font-family: var(--font-display);
    font-weight: 600;
    font-size: 1rem;
    letter-spacing: 0.08em;
    color: var(--charcoal-uniform);
  }

  .divider-line {
    width: 1px;
    height: 0.85rem;
    background: var(--hairline);
  }

  .romaji-name {
    font-size: 0.85rem;
    font-weight: 500;
    color: var(--wolf-ash);
  }

  .top-controls {
    display: flex;
    align-items: center;
    gap: 0.65rem;
    flex-wrap: wrap;
  }

  .time-pill {
    display: inline-flex;
    align-items: center;
    gap: 0.45rem;
    padding: 0.4rem 0.85rem;
    font-size: 0.8rem;
    color: var(--wolf-ash);
    background: var(--panel-surface);
    backdrop-filter: blur(14px);
    border-radius: 999px;
    border: 1px solid rgba(255, 255, 255, 0.65);
  }

  .sun-dot {
    width: 7px;
    height: 7px;
    border-radius: 50%;
    background: #e296a6;
  }

  .bg-switcher {
    display: inline-flex;
    padding: 0.2rem;
    background: var(--panel-surface);
    backdrop-filter: blur(14px);
    border-radius: 999px;
    border: 1px solid rgba(255, 255, 255, 0.65);
  }

  .bg-switcher button {
    padding: 0.28rem 0.72rem;
    font-size: 0.78rem;
    border-radius: 999px;
    color: var(--wolf-ash);
    transition: background-color 0.2s ease, color 0.2s ease;
  }

  .bg-switcher button.active {
    background: var(--panel-surface-strong);
    color: var(--charcoal-uniform);
    font-weight: 500;
    box-shadow: 0 1px 6px rgba(35, 33, 38, 0.06);
  }

  .clear-toggle {
    padding: 0.42rem 0.9rem;
    font-size: 0.8rem;
    font-weight: 500;
    border-radius: 999px;
    background: var(--panel-surface);
    backdrop-filter: blur(14px);
    border: 1px solid rgba(255, 255, 255, 0.7);
    color: var(--charcoal-uniform);
    transition: background-color 0.2s ease, transform 0.2s ease;
  }

  .clear-toggle:hover {
    background: var(--panel-surface-strong);
  }

  .clear-toggle.active {
    background: var(--charcoal-uniform);
    color: var(--sakura-mist);
    border-color: var(--charcoal-uniform);
  }

  /* Workspace & Personal Sheet */
  .workspace {
    flex: 1;
    display: flex;
    align-items: center;
    justify-content: flex-start;
    min-height: 0;
    padding: 0.75rem 0;
    transition: opacity 0.35s ease, transform 0.35s ease;
  }

  .workspace.is-hidden {
    opacity: 0;
    pointer-events: none;
    transform: translateY(8px);
  }

  .personal-sheet {
    width: min(100%, 510px);
    max-height: calc(100dvh - 7.5rem);
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    padding: 1.75rem 2rem;
    background: var(--panel-surface);
    backdrop-filter: blur(20px) saturate(1.1);
    -webkit-backdrop-filter: blur(20px) saturate(1.1);
    border: 1px solid rgba(255, 255, 255, 0.78);
    border-radius: 22px;
    box-shadow:
      0 20px 50px rgba(45, 36, 42, 0.08),
      0 1px 0 rgba(255, 255, 255, 0.9) inset;
  }

  .sheet-header {
    margin-bottom: 1.1rem;
  }

  .portrait-row {
    display: flex;
    align-items: center;
    gap: 0.95rem;
    margin-bottom: 0.85rem;
  }

  .mini-avatar {
    width: 54px;
    height: 54px;
    border-radius: 50%;
    object-fit: cover;
    border: 2px solid rgba(255, 255, 255, 0.9);
    box-shadow: 0 4px 12px rgba(35, 33, 38, 0.08);
    flex-shrink: 0;
  }

  .role-line {
    font-size: 0.8rem;
    color: var(--iris-blue);
    font-weight: 500;
    margin-bottom: 0.15rem;
  }

  h1 {
    font-family: var(--font-display);
    font-size: clamp(1.65rem, 2.3vw, 2.1rem);
    font-weight: 600;
    line-height: 1.15;
    letter-spacing: -0.01em;
    color: var(--charcoal-uniform);
  }

  .personal-quote {
    font-family: var(--font-display);
    font-size: 0.93rem;
    line-height: 1.6;
    color: #3d3a45;
    padding-left: 0.85rem;
    border-left: 2px solid var(--blossom-blush);
    max-width: 58ch;
  }

  /* Navigation Tabs */
  .sheet-tabs {
    display: flex;
    gap: 0.35rem;
    padding-bottom: 0.75rem;
    margin-bottom: 1rem;
    border-bottom: 1px solid var(--hairline);
  }

  .sheet-tabs button {
    padding: 0.4rem 0.8rem;
    font-size: 0.83rem;
    font-weight: 500;
    color: var(--wolf-ash);
    border-radius: 10px;
    transition: background-color 0.18s ease, color 0.18s ease;
  }

  .sheet-tabs button:hover {
    color: var(--charcoal-uniform);
    background: rgba(255, 255, 255, 0.5);
  }

  .sheet-tabs button.selected {
    color: var(--charcoal-uniform);
    background: var(--panel-surface-strong);
    box-shadow: 0 2px 8px rgba(35, 33, 38, 0.05);
  }

  /* Tab Content Stage */
  .tab-stage {
    min-height: 235px;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
  }

  .prose-paragraph {
    font-size: 0.88rem;
    line-height: 1.62;
    color: var(--charcoal-uniform);
    margin-bottom: 1rem;
    max-width: 60ch;
  }

  .natural-facts {
    display: grid;
    grid-template-columns: repeat(2, minmax(0, 1fr));
    gap: 0.65rem 1rem;
    margin-bottom: 1rem;
    padding: 0.8rem 0.95rem;
    background: rgba(255, 255, 255, 0.55);
    border-radius: 14px;
    border: 1px solid rgba(255, 255, 255, 0.7);
  }

  .fact-item dt {
    font-size: 0.73rem;
    color: var(--wolf-ash);
    margin-bottom: 0.12rem;
  }

  .fact-item dd {
    font-size: 0.82rem;
    font-weight: 500;
    color: var(--charcoal-uniform);
    line-height: 1.35;
  }

  .status-box {
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 0.85rem;
    padding: 0.75rem 0.95rem;
    background: var(--iris-blue-soft);
    border-radius: 14px;
  }

  .status-info {
    display: flex;
    flex-direction: column;
    gap: 0.18rem;
  }

  .status-heading {
    font-size: 0.76rem;
    font-weight: 600;
    color: var(--iris-blue);
  }

  .status-detail {
    font-size: 0.82rem;
    color: var(--charcoal-uniform);
    line-height: 1.35;
  }

  .status-drink {
    font-size: 0.75rem;
    color: var(--wolf-ash);
  }

  .gentle-btn {
    padding: 0.42rem 0.82rem;
    font-size: 0.78rem;
    font-weight: 500;
    border-radius: 10px;
    background: var(--panel-surface-strong);
    color: var(--charcoal-uniform);
    border: 1px solid var(--hairline);
    white-space: nowrap;
    transition: background-color 0.18s ease, border-color 0.18s ease;
  }

  .gentle-btn:hover {
    background: #ffffff;
    border-color: rgba(35, 33, 38, 0.2);
  }

  .gentle-btn.primary {
    background: var(--iris-blue);
    color: #ffffff;
    border-color: var(--iris-blue);
  }

  .gentle-btn.primary:hover {
    background: #426b91;
  }

  /* Notes Tab */
  .quick-note-form {
    display: flex;
    gap: 0.5rem;
    margin-bottom: 0.8rem;
  }

  .quick-note-form input,
  .desk-form input,
  .desk-form textarea {
    width: 100%;
    padding: 0.48rem 0.75rem;
    font-size: 0.83rem;
    border-radius: 10px;
    border: 1px solid var(--hairline);
    background: rgba(255, 255, 255, 0.75);
  }

  .notes-list {
    list-style: none;
    display: flex;
    flex-direction: column;
    gap: 0.55rem;
  }

  .note-item {
    padding: 0.65rem 0.85rem;
    background: rgba(255, 255, 255, 0.62);
    border-radius: 12px;
    border: 1px solid rgba(255, 255, 255, 0.8);
  }

  .note-meta {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 0.22rem;
    font-size: 0.73rem;
  }

  .note-tag {
    color: var(--iris-blue);
    font-weight: 600;
  }

  .note-date {
    color: var(--wolf-ash);
  }

  .note-body {
    font-size: 0.82rem;
    line-height: 1.45;
    color: var(--charcoal-uniform);
  }

  /* Likes Tab */
  .likes-grid {
    display: grid;
    grid-template-columns: repeat(2, minmax(0, 1fr));
    gap: 0.65rem;
  }

  .like-entry {
    padding: 0.75rem 0.85rem;
    background: rgba(255, 255, 255, 0.62);
    border-radius: 13px;
    border: 1px solid rgba(255, 255, 255, 0.8);
  }

  .like-cat {
    display: block;
    font-size: 0.72rem;
    color: var(--iris-blue);
    font-weight: 500;
    margin-bottom: 0.2rem;
  }

  .like-entry h2 {
    font-family: var(--font-display);
    font-size: 0.9rem;
    font-weight: 600;
    margin-bottom: 0.25rem;
    line-height: 1.3;
  }

  .like-entry p {
    font-size: 0.79rem;
    line-height: 1.42;
    color: var(--wolf-ash);
  }

  /* Desk Tab */
  .desk-intro {
    font-size: 0.84rem;
    line-height: 1.5;
    color: var(--wolf-ash);
    margin-bottom: 0.65rem;
  }

  .desk-form {
    display: flex;
    flex-direction: column;
    gap: 0.5rem;
    margin-bottom: 0.65rem;
  }

  .form-row {
    display: flex;
    gap: 0.5rem;
  }

  .desk-form textarea {
    resize: none;
  }

  .confirm-banner {
    font-size: 0.78rem;
    color: var(--iris-blue);
    font-weight: 500;
    margin-bottom: 0.45rem;
  }

  .recent-memos {
    display: flex;
    flex-direction: column;
    gap: 0.45rem;
  }

  .memo-slip {
    padding: 0.6rem 0.8rem;
    background: rgba(255, 255, 255, 0.64);
    border-radius: 11px;
    border-left: 3px solid var(--iris-blue);
  }

  .memo-top {
    display: flex;
    justify-content: space-between;
    font-size: 0.75rem;
    margin-bottom: 0.15rem;
  }

  .memo-top span {
    color: var(--wolf-ash);
  }

  .memo-slip p {
    font-size: 0.81rem;
    line-height: 1.4;
  }

  /* Bottom Bar */
  .bottom-bar {
    display: flex;
    align-items: center;
    justify-content: space-between;
    z-index: 2;
    flex-shrink: 0;
  }

  .season-caption {
    font-size: 0.78rem;
    color: var(--wolf-ash);
    padding: 0.3rem 0.75rem;
    background: rgba(251, 247, 246, 0.7);
    backdrop-filter: blur(8px);
    border-radius: 999px;
  }

  .restore-pill {
    padding: 0.48rem 1.05rem;
    font-size: 0.82rem;
    font-weight: 500;
    border-radius: 999px;
    background: var(--panel-surface-strong);
    color: var(--charcoal-uniform);
    box-shadow: 0 6px 20px rgba(35, 33, 38, 0.12);
  }

  /* Responsive adjustment for smaller screens */
  @media (max-width: 899px) {
    .viewport-shell {
      padding: 1rem;
      overflow-y: auto;
    }

    .personal-sheet {
      max-height: none;
      width: 100%;
      margin-top: 42vh;
    }

    .bg-character-wrap {
      justify-content: center;
      align-items: flex-start;
      padding-right: 0;
    }

    .bg-character-img {
      max-width: 100vw;
      height: 62vh;
      object-position: center top;
      mask-image: linear-gradient(to bottom, black 65%, transparent 100%);
      -webkit-mask-image: linear-gradient(to bottom, black 65%, transparent 100%);
    }
  }
</style>
