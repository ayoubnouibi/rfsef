1. Changement HTML dans app-chat-page (chat-page.component.html)

Dans le code d'origine, .landing-composer est en dehors de .landing-scroll, ce qui l'épingle en bas du viewport et empêche d'y accéder sous zoom. Il faut le placer dans .landing-inner, juste après .landing-disclaimer :

html
<!-- AVANT -->
<section class="landing">
  <div class="landing-scroll">
    <div class="landing-inner">
      ...
      <section class="landing-disclaimer" role="note">...</section>
    </div>
  </div>
  <div class="landing-composer">
    <app-chat-input placeholderKey="landing.placeholder" />
  </div>
</section>
<!-- APRÈS -->
<section class="landing">
  <div class="landing-scroll">
    <div class="landing-inner">
      ...
      <section class="landing-disclaimer" role="note">...</section>
      <div class="landing-composer">
        <app-chat-input placeholderKey="landing.placeholder" />
      </div>
    </div>
  </div>
</section>

(Cela ne touche que la vue d'accueil .landing ; la vue .messages + .conversation-composer conserve son comportement habituel).

2. src/styles.css (Variables globales & échelle rem)
Sélecteur	Propriété	Avant	Après
:root	--muted	#64748b	#475569
:root	--sidebar-w	(inexistant)	14rem
:root	--tap	(inexistant)	2.75rem (44px)
:root	--gutter	(inexistant)	1rem
:root	--col	(inexistant)	36rem
:root	--h1	(inexistant)	1.875rem
html	font-size	(implicite)	100% (+ paliers 112.5%, 125%, 150% via @media)
:focus-visible	outline	(inexistant)	0.1875rem solid var(--redion-navy); outline-offset: 0.125rem
3. app-chat-page (chat-page.component.css)
Sélecteur	Propriété	Avant (code Angular)	Après
.app	grid-template-columns	184px 1fr	var(--sidebar-w) 1fr
.app	height / overflow	height: 100vh; height: 100dvh; overflow: hidden	min-height: 100vh; min-height: 100dvh (ou garder overflow: hidden uniquement quand une conversation est active)
.main	height / overflow	height: 100%; overflow: hidden	min-height: 100vh; min-height: 100dvh
.topbar	height / padding	height: 44px; padding: 0 24px	min-height: 2.75rem; padding: 0.75rem var(--gutter)
.lang__btn	padding / font-size / min-height	padding: 6px 10px; font-size: 13px	min-height: var(--tap); padding: 0 0.9rem; font-size: 0.875rem
.landing-scroll	padding	40px 40px 12px	clamp(0.5rem, 3vh, 2rem) var(--gutter) 0
.landing-scroll::after	content / height	(inexistant)	content: ""; display: block; flex: none; height: max(6rem, 65dvh) (marge de scroll garantissant l'accès à la saisie sous n'importe quel zoom)
.landing-inner	width / margin	width: min(760px, 100%); margin: auto 0	width: 100%; max-width: var(--col); margin: 0 auto (suppression de margin: auto 0 qui centrait verticalement)
.status-pill	padding / font-size / margin-bottom	padding: 6px 12px; font-size: 12px; margin-bottom: 18px	padding: 0.4rem 0.9rem; font-size: 0.875rem; margin-bottom: 1rem
h1	font-size / line-height / letter-spacing / text-wrap	font-size: clamp(30px, 4vw, 48px); line-height: 1.08; letter-spacing: -1.5px	font-size: var(--h1); line-height: 1.12; letter-spacing: -0.03em; text-wrap: balance
h1 .accent	white-space	(inexistant)	white-space: nowrap (empêche la coupure « 2- / minute »)
.subtitle	margin / font-size / max-width	margin: 14px auto 34px; font-size: 17px; max-width: 590px	margin: 0.75rem auto 1rem; font-size: 1.125rem; max-width: var(--col)
.landing-disclaimer	max-width / margin / padding / font-size	max-width: 590px; margin: -14px auto 28px; padding: 14px 16px; font-size: 13px	max-width: var(--col); margin: 0 auto 1rem; padding: 1rem 1.25rem; font-size: 0.9375rem; line-height: 1.55
.landing-disclaimer__privacy a	display / padding	(inline par défaut)	display: inline-block; padding: 0.5rem 0
.landing-composer	padding / max-width	padding: 8px 40px 12px	padding: 0.5rem 0; width: 100%; max-width: var(--col)
.powered	font-size / color	font-size: 11px; color: #94a3b8	font-size: 0.8125rem; color: var(--muted)
4. app-chat-sidebar (chat-sidebar.component.css)
Sélecteur	Propriété	Avant (code Angular)	Après
:host	position / height	display: block; height: 100vh	display: block; position: sticky; top: 0; height: 100vh; height: 100dvh
.sidebar	padding / gap	padding: 16px 12px; gap: 14px	padding: 1rem; gap: 1rem
.new-chat	height / border-radius / font-size / padding	height: 36px; border-radius: 999px 999px 4px; font-size: 12px	min-height: var(--tap); border-radius: 62.5rem; font-size: 0.9rem; padding: 0.5rem 1rem; white-space: nowrap (corrige le coin inférieur droit à 4px)
.human-button	height / border-radius	height: 28px; border-radius: 999px 999px 4px	min-height: 2.25rem; border-radius: 62.5rem
.safe-card	padding / border-radius	padding: 10px; border-radius: 10px	padding: 0.75rem; border-radius: 0.5rem
.safe-head	font-size / gap	font-size: 12px; gap: 7px	font-size: 0.875rem; gap: 0.5rem
.safe-card p	font-size	font-size: 10px	font-size: 0.8125rem
5. app-chat-input (chat-input.component.css)
Sélecteur	Propriété	Avant (code Angular)	Après
.composer	min-height / border-radius / padding / gap	min-height: 58px; border-radius: 999px 999px 8px; padding: 6px 8px 6px 18px; gap: 6px	border-radius: 1.75rem; padding: 0.4rem 0.4rem 0.4rem 1.1rem; gap: 0.5rem; align-items: flex-end (corrige le coin inférieur droit à 8px)
.composer:focus-within	border-color	(inexistant)	border-color: var(--redion-navy)
.composer__input	font-size / max-height / padding	font-size: 15px; max-height: 140px; padding: 8px 0	font-size: 1rem; max-height: 8rem; padding: 0.65rem 0 (≥ 16px évite le zoom auto iOS)
.composer__input::placeholder	color	#8b95a5	#64748b
.composer__send	width / height	width: 36px; height: 36px	width: var(--tap); height: var(--tap) (2.75rem / 44px)
.composer__send:disabled	opacity / background	opacity: .45	background: #d9a3a1; opacity: 1
6. Media queries remplacées / ajoutées (en rem)

Dans le code Angular d'origine, il n'y avait que @media (max-width: 820px) :

css
/* Paliers de largeur (mobile-first) — dans styles.css */
@media (min-width: 30rem)  { :root { --gutter: 1.25rem; --h1: 2.125rem; } }                            /* ≥ 480px */
@media (min-width: 48rem)  { :root { --col: 40rem; --h1: 2.5rem; --gutter: 1.5rem; } }                 /* ≥ 768px */
@media (min-width: 64rem)  { :root { --col: 44rem; --h1: 2.75rem; --sidebar-w: 15rem; } }              /* ≥ 1024px */
@media (min-width: 90rem)  { html { font-size: 112.5%; } :root { --col: 48rem; --sidebar-w: 16rem; } } /* ≥ 1440px (base 18px) */
@media (min-width: 120rem) { html { font-size: 125%; }   :root { --col: 52rem; } }                     /* ≥ 1920px (base 20px) */
@media (min-width: 160rem) { html { font-size: 150%; } }                                               /* ≥ 2560px (base 24px) */
/* Tablette & Mobile — dans chat-page.component.css (remplace max-width: 820px) */
@media (max-width: 51.25rem) {
  .app { grid-template-columns: 1fr; }
  .app--menu-open app-chat-sidebar { width: min(82vw, 18.75rem); }
}
/* Petits mobiles (≤ 480px) */
@media (max-width: 30rem) {
  .lang__name { display: none; }
  .landing-disclaimer { font-size: 0.8125rem; line-height: 1.45; padding: 0.75rem 1rem; }
  .subtitle { font-size: 1rem; }
}
/* Hauteur réduite & paysage */
@media (max-height: 56.25rem) {
  .subtitle { font-size: 1rem; }
  .landing-disclaimer { font-size: 0.875rem; }
}
@media (max-height: 43.75rem) {
  .status-pill, .subtitle, .powered, .safe-card { display: none; }
  h1 { font-size: calc(var(--h1) * 0.8); }
}
@media (max-height: 30rem) {
  .topbar, .mobile-header { padding-block: 0.25rem; height: auto; }
  .landing-scroll { padding-top: 0.25rem; }
}
