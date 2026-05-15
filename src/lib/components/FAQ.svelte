<script lang="ts">
  import { onMount } from 'svelte';
  
  interface FAQItem {
    q: string;
    a: string;
    open: boolean;
  }

  let faqs = $state<FAQItem[]>([
    { q: "Where can I buy Cards Against Humanity?", a: "You can buy Cards Against Humanity at many major retailers or directly from our website. The base game and all expansions are available online.", open: false },
    { q: "How do I play Cards Against Humanity?", a: "One player asks a question from a black card, and everyone else answers with their funniest white card. The question czar picks the best answer, and that player wins the round.", open: false },
    { q: "I bought something from you and I love it / there's a problem.", a: "We're thrilled (or sorry)! Contact our customer support team and we'll make it right. We stand behind everything we sell.", open: false },
    { q: "Is Cards Against Humanity available for families/children?", a: "Absolutely not. Cards Against Humanity is intended for adults only. It contains mature humor that is not appropriate for children.", open: false },
    { q: "Can I make my own Cards Against Humanity?", a: "Yes! We release a free printable version of the game on our website. Download it, print it, cut out the cards, and you're ready to play.", open: false },
    { q: "Do you make a Cards Against Humanity app?", a: "We do not have an official app. Any apps claiming to be Cards Against Humanity are unauthorized. Stick to the real thing!", open: false },
    { q: "Can I play Cards Against Humanity online, anywhere?", a: "There are fan-made platforms like All Bad Cards and Pretend You're Xyzzy that let you play online with friends.", open: false },
    { q: "I have some really good card suggestions.", a: "That's sweet! We get thousands of suggestions and can't review them all, but we appreciate the creativity.", open: false },
  ]);

  function toggle(index: number) {
    faqs = faqs.map((f, i) => ({ ...f, open: i === index ? !f.open : false }));
  }
</script>

<div class="faq-list">
  {#each faqs as faq, i (i)}
    <div class="faq-item" class:open={faq.open}>
      <button
        class="faq-question"
        onclick={() => toggle(i)}
        data-cursor={faq.open ? 'CLOSE' : 'OPEN'}
        aria-expanded={faq.open}
        id="faq-btn-{i}"
      >
        <span>{faq.q}</span>
        <span class="faq-icon" class:rotated={faq.open}>+</span>
      </button>
      <div class="faq-answer" style="max-height: {faq.open ? '200px' : '0'}">
        <div class="faq-answer-inner">
          <p>{faq.a}</p>
        </div>
      </div>
    </div>
  {/each}
</div>

<style>
  .faq-list {
    width: 100%;
    max-width: 700px;
    margin: 0 auto;
  }

  .faq-item {
    border-bottom: 2px solid rgba(10,10,10,0.12);
    overflow: hidden;
  }

  .faq-item:first-child {
    border-top: 2px solid rgba(10,10,10,0.12);
  }

  .faq-question {
    width: 100%;
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 16px;
    padding: 20px 4px;
    background: none;
    border: none;
    font-family: inherit;
    font-size: 15px;
    font-weight: 600;
    color: #0a0a0a;
    text-align: left;
    transition: color 0.2s ease;
    max-width: 700px;
    width: 100%;
  }

  .faq-item.open .faq-question {
    color: #e8003a;
  }

  .faq-icon {
    font-size: 24px;
    font-weight: 300;
    flex-shrink: 0;
    transition: transform 0.3s cubic-bezier(0.34, 1.56, 0.64, 1), color 0.2s ease;
    color: #0a0a0a;
  }

  .faq-item.open .faq-icon {
    color: #e8003a;
  }

  .faq-icon.rotated {
    transform: rotate(45deg);
  }

  .faq-answer {
    overflow: hidden;
    transition: max-height 0.4s cubic-bezier(0.4, 0, 0.2, 1);
  }

  .faq-answer-inner {
    padding: 0 4px 20px 4px;
    font-size: 14px;
    line-height: 1.7;
    color: #444;
  }
</style>
