<script lang="ts">
  import { onDestroy, onMount } from 'svelte'

  const installHref = 'https://chromewebstore.google.com/'
  const heroBlobSizes = [56, 78, 64]
  const heroBlobInnerSizes = [15, 22, 17]
  const heroBlobOpacities = [0.58, 0.4, 0.3]

  let heroCtaButton: HTMLAnchorElement | null = null
  let heroCtaActive = false
  let heroBlobPositions = heroBlobSizes.map(() => ({ x: 0, y: 0 }))
  let heroBlobTarget = { x: 0, y: 0 }
  let heroBlobFrame = 0

  function centerHeroBlob() {
    if (!heroCtaButton) return

    const rect = heroCtaButton.getBoundingClientRect()
    const x = rect.width / 2
    const y = rect.height / 2

    heroBlobTarget = { x, y }
    heroBlobPositions = heroBlobSizes.map(() => ({ x, y }))
  }

  function animateHeroBlob() {
    if (!heroCtaButton) {
      heroBlobFrame = 0
      return
    }

    heroBlobPositions = heroBlobPositions.map((position, index, positions) => {
      const lead = index === 0 ? heroBlobTarget : positions[index - 1]
      const easing = index === 0 ? 0.34 : index === 1 ? 0.08 : 0.045

      return {
        x: position.x + (lead.x - position.x) * easing,
        y: position.y + (lead.y - position.y) * easing,
      }
    })

    heroBlobFrame = window.requestAnimationFrame(animateHeroBlob)
  }

  function startHeroBlob() {
    if (heroBlobFrame) return
    heroBlobFrame = window.requestAnimationFrame(animateHeroBlob)
  }

  function handleHeroCtaEnter(event: MouseEvent) {
    heroCtaActive = true
    handleHeroCtaMove(event)
    startHeroBlob()
  }

  function handleHeroCtaMove(event: MouseEvent) {
    if (!heroCtaButton) return

    const rect = heroCtaButton.getBoundingClientRect()
    heroBlobTarget = {
      x: event.clientX - rect.left,
      y: event.clientY - rect.top,
    }
  }

  function handleHeroCtaLeave() {
    heroCtaActive = false
  }

  onMount(() => {
    centerHeroBlob()
  })

  onDestroy(() => {
    if (heroBlobFrame) {
      window.cancelAnimationFrame(heroBlobFrame)
    }
  })

  const navLinks = [
    { label: 'Как работи', href: '#workflow' },
    { label: 'Възможности', href: '#capabilities' },
    { label: 'Поверителност', href: '#privacy' },
  ]

  const proofItems = [
    {
      badge: '3 нива на проверка',
      title: 'Структура, източници и качество на твърдението в един преглед',
      description:
        'Verity Lens преглежда самата статия, цитираните доказателства и начина, по който е рамкирано твърдението, преди да подаде предупредителен сигнал.',
    },
    {
      badge: '<5 сек. преглед',
      title: 'Достатъчно бързо за таба, който вече е отворен',
      description:
        'Първият прочит е създаден за бързи новинарски моменти, така че съмнението да се появи преди социалното потвърждение да надделее.',
    },
    {
      badge: '1 клик до източника',
      title: 'Всяко предупреждение сочи към нещо конкретно',
      description:
        'Вместо неясни оценки, разширението показва кой източник е слаб, липсва, повтаря се или е използван извън реалния му контекст.',
    },
  ]

  const workflowSteps = [
    {
      title: 'Открийте слабото твърдение',
      description:
        'Маркирайте формулировки, които преувеличават сигурността, прикриват източника или обещават повече, отколкото текстът може да защити.',
      bullets: [
        'Прекалено категорични формулировки',
        'Анонимни или липсващи източници',
        'Разминаване между заглавие и съдържание',
      ],
    },
    {
      title: 'Сравнете пътя на източника',
      description:
        'Подредете историята срещу свързаните доказателства и липсващите препратки, за да стане ясно кое е надеждно, кое е повторено и кое остава непокрито.',
      bullets: [
        'Първични срещу преписани цитати',
        'Липсващи или счупени препратки',
        'Конфликт в обхват, дата или контекст',
      ],
    },
    {
      title: 'Разберете защо е важно',
      description:
        'Превърнете предупреждението в ясен контекст, който помага да се прецени дали историята изисква още проверка, по-внимателно четене или спиране на споделянето.',
      bullets: ['Кратки обяснителни наслагвания', 'Сигнали за риск в контекст', 'Бърз път към по-добри източници'],
    },
  ]

  const capabilities = [
    {
      label: 'Доказателство',
      tone: 'cobalt',
      title: 'Проверка на източника',
      description:
        'Тества дали посоченият източник наистина подкрепя твърдението, вместо просто да звучи авторитетно.',
      bullets: ['Проверява наличността на източника', 'Маркира повторно използвани цитати'],
    },
    {
      label: 'Рамка',
      tone: 'teal',
      title: 'Сигнали за пристрастие и рамкиране',
      description:
        'Показва кога езикът повишава сигурността или натоварва емоционално темата, преди доказателствата да са ясни.',
      bullets: ['Отклонение между заглавие и текст', 'Сигнали за натоварена формулировка'],
    },
    {
      label: 'Следа',
      tone: 'amber',
      title: 'Път на цитиране',
      description:
        'Връща читателя към първичния материал, така че преходът от история към източник да става с един клик, а не през шест таба.',
      bullets: ['Групира свързаните препратки', 'Посочва липсващите линкове'],
    },
    {
      label: 'Контекст',
      tone: 'ink',
      title: 'Обяснителни наслагвания',
      description:
        'Оставя сигнала до самата статия с кратки пояснения, които се вписват в реалния ритъм на четене, без отделно табло.',
      bullets: ['Спътник по време на четене', 'Кратки обяснения за бърз преглед'],
    },
  ]

  const privacyPromises = [
    'Стартира първо за Chrome с директен път към инсталиране и спокойна, ясна CTA логика.',
    'Езикът за поверителност остава точен: без фалшиви препоръки, без измислен авторитет и без тъмни модели.',
    'Контекстът остава до страницата, така че крайното решение остава у читателя.',
  ]
</script>

<a class="skip-link" href="#main">Пропусни към основното съдържание</a>

<div class="site-shell">
  <header class="site-header">
    <div class="site-header__inner">
      <a class="brand" href="#top" aria-label="Начало на Verity Lens">
        <span class="brand-mark" aria-hidden="true">
          <span class="brand-mark__core"></span>
          <span class="brand-mark__ring"></span>
          <span class="brand-mark__beam"></span>
        </span>
        <span class="brand-lockup">
          <strong>Verity Lens</strong>
          <span>Проверка на факти в реално време</span>
        </span>
      </a>

      <nav class="site-nav" aria-label="Основна навигация">
        {#each navLinks as link}
          <a href={link.href}>{link.label}</a>
        {/each}
      </nav>

      <a
        class="button button--ghost header-cta"
        href={installHref}
        target="_blank"
        rel="noreferrer"
      >
        Инсталирай за Chrome
      </a>
    </div>
  </header>

  <main id="main">
    <section class="hero section" id="top">
      <div class="hero__panel">
        <div class="hero-copy">
          <div class="eyebrow">
            <span class="eyebrow__dot"></span>
            Разширение за Chrome за проверка на факти
          </div>
          <h1>Проверете фактите, преди да повярвате.</h1>
          <p class="hero-lede">
            Verity Lens е разширение за Chrome, което проверява източници, открива
            отклонения в твърденията и добавя контекст още докато четете.
          </p>

          <div class="hero-actions">
            <a
              bind:this={heroCtaButton}
              class:button--blob-active={heroCtaActive}
              class="button button--primary"
              href={installHref}
              target="_blank"
              rel="noreferrer"
              onmouseenter={handleHeroCtaEnter}
              onmousemove={handleHeroCtaMove}
              onmouseleave={handleHeroCtaLeave}
            >
              <span class="blob-container hero-cta-blob-layer" aria-hidden="true">
                <svg class="hero-cta-blob-filter" focusable="false" aria-hidden="true">
                  <filter id="hero-cta-blob-goo">
                    <feGaussianBlur in="SourceGraphic" result="blur" stdDeviation="9" />
                    <feColorMatrix
                      in="blur"
                      values="1 0 0 0 0 0 1 0 0 0 0 0 1 0 0 0 0 0 20 -6"
                    />
                  </filter>
                </svg>

                <span class="blob-main">
                  {#each heroBlobPositions as blob, index}
                    <span
                      class="blob hero-cta-blob"
                      style={`width:${heroBlobSizes[index]}px;height:${heroBlobSizes[index]}px;opacity:${heroBlobOpacities[index]};left:${blob.x}px;top:${blob.y}px;`}
                    >
                      <span
                        class="inner-dot hero-cta-inner-dot"
                        style={`width:${heroBlobInnerSizes[index]}px;height:${heroBlobInnerSizes[index]}px;top:${(heroBlobSizes[index] - heroBlobInnerSizes[index]) / 2}px;left:${(heroBlobSizes[index] - heroBlobInnerSizes[index]) / 2}px;`}
                      ></span>
                    </span>
                  {/each}
                </span>
              </span>
              <span class="hero-cta-label">Инсталирай за Chrome</span>
            </a>
            <a class="hero-text-link" href="#workflow">Как работи</a>
          </div>
        </div>

        <div class="hero-visual" aria-hidden="true">
          <div class="hero-signal">
            <div class="hero-signal__glow"></div>
            <div class="hero-signal__panel">
              <div class="hero-signal__headline">
                <p>Статията изисква допълнителна проверка</p>
                <div class="hero-signal__score">
                  <strong>Надеждност 41 / 100</strong>
                  <div class="hero-signal__meter" role="presentation">
                    <span class="hero-signal__meter-fill"></span>
                  </div>
                </div>
              </div>

              <p class="hero-signal__summary">
                Материалът използва уверен тон, но не показва ясен първичен документ, който да
                подкрепя основното твърдение. Заглавието обещава по-силен извод, отколкото
                съдържанието може да защити с наличните доказателства.
              </p>

              <div class="hero-signal__list">
                <article class="hero-signal__flag">
                  <span>Червен флаг</span>
                  <strong>Липсва първичен документ</strong>
                  <small>Твърдението се опира на вторични препратки без проверим оригинал.</small>
                </article>

                <article class="hero-signal__flag">
                  <span>Червен флаг</span>
                  <strong>Заглавието надхвърля текста</strong>
                  <small>Нивото на сигурност в headline-а е по-високо от това в самата статия.</small>
                </article>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>

    <section class="proof section" aria-labelledby="proof-heading">
      <div class="section-heading">
        <p class="section-kicker">Процес, а не шум</p>
        <h2 id="proof-heading">Създаден да показва как мисли разширението, а не да симулира сигурност.</h2>
        <p>
          Всеки доказателствен елемент е формулиран като продуктова способност, а не като
          измислена популярност, така че страницата да изглежда достоверна още преди
          разширението да е живо.
        </p>
      </div>

      <div class="proof-grid">
        {#each proofItems as item}
          <article class="proof-card">
            <p class="proof-card__badge">{item.badge}</p>
            <h3>{item.title}</h3>
            <p>{item.description}</p>
          </article>
        {/each}
      </div>
    </section>

    <section class="workflow section" id="workflow" aria-labelledby="workflow-heading">
      <div class="section-heading">
        <p class="section-kicker">Как работи</p>
        <h2 id="workflow-heading">
          По-бърз начин да поставите под съмнение слабата информация, без да напускате страницата.
        </h2>
        <p>
          Разширението е подредено според реалния ритъм на четене: открива проблема,
          сравнява доказателствата и обяснява риска с ясен език.
        </p>
      </div>

      <div class="workflow-grid">
        {#each workflowSteps as step, index}
          <article class="workflow-card">
            <div class="workflow-card__number">0{index + 1}</div>
            <h3>{step.title}</h3>
            <p>{step.description}</p>
            <ul>
              {#each step.bullets as bullet}
                <li>{bullet}</li>
              {/each}
            </ul>
          </article>
        {/each}
      </div>
    </section>

    <section class="capabilities section" id="capabilities" aria-labelledby="capabilities-heading">
      <div class="section-heading">
        <p class="section-kicker">Възможности</p>
        <h2 id="capabilities-heading">
          Сигнали за читателя, които остават полезни и в най-натоварените новинарски моменти.
        </h2>
        <p>
          Verity Lens се фокусира върху моментите, които действително променят преценката:
          слаби източници, отклонение в рамкирането, липсващи цитати и кратки обяснения,
          които се вписват в самата статия.
        </p>
      </div>

      <div class="capabilities-grid">
        {#each capabilities as capability}
          <article class={`capability-card capability-card--${capability.tone}`}>
            <p class="capability-card__label">{capability.label}</p>
            <h3>{capability.title}</h3>
            <p>{capability.description}</p>
            <ul>
              {#each capability.bullets as bullet}
                <li>{bullet}</li>
              {/each}
            </ul>
          </article>
        {/each}
      </div>
    </section>

    <section class="closing section" id="privacy" aria-labelledby="privacy-heading">
      <div class="closing__frame">
        <div class="closing__copy">
          <div class="section-heading section-heading--compact">
            <p class="section-kicker">Поверителност и старт</p>
            <h2 id="privacy-heading">Създаден да добавя контекст, а не още шум.</h2>
            <p>
              Историята на продукта остава ясна: първо за Chrome, фокус върху инсталирането,
              уважение към читателя и точни твърдения още преди разширението да е готово за широк старт.
            </p>
          </div>

          <ul class="closing__list">
            {#each privacyPromises as promise}
              <li>{promise}</li>
            {/each}
          </ul>

          <div class="closing__actions">
            <a class="button button--primary" href={installHref} target="_blank" rel="noreferrer">
              Инсталирай за Chrome
            </a>
            <a class="closing__text-link" href="#capabilities">Прегледай сигналите</a>
          </div>
        </div>

        <aside class="closing__panel">
          <p class="closing__eyebrow">Уважение към читателя</p>

          <div class="closing__stack">
            <article>
              <strong>Контекст на място</strong>
              <span>Предупрежденията стоят до статията, така че историята и сигналът да останат свързани.</span>
            </article>
            <article>
              <strong>Ясни основания</strong>
              <span>Слабите източници, отклоненията и липсващите доказателства се обясняват с разбираем език.</span>
            </article>
            <article>
              <strong>Контрол за читателя</strong>
              <span>Разширението ускорява проверката, без да претендира, че замества личната преценка.</span>
            </article>
          </div>
        </aside>
      </div>
    </section>
  </main>

  <footer class="site-footer">
    <p>Verity Lens. Сигнали за достоверност за модерното четене.</p>
  </footer>
</div>
