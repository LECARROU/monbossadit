---
title: "Soumettre une anecdote"
slug: "submit"
layout: "single"
hideMeta: true

---

<form id="anecdoteForm">
  <div class="field">
    <label for="anecdote">Ton anecdote *</label>
    <textarea id="anecdote" name="anecdote" 
              placeholder="Mon boss a dit que..." 
              maxlength="500" rows="5" required></textarea>
    <span id="compteur">0/500</span>
  </div>

  <div class="field">
    <label for="pseudo">Pseudo (optionnel)</label>
    <input type="text" id="pseudo" name="pseudo" 
           placeholder="Anonyme" maxlength="30">
  </div>

  <button type="submit" id="submitBtn">Envoyer mon anecdote</button>
  <p id="message"></p>
</form>

<style>
.field { margin: 1.2rem 0; display: flex; flex-direction: column; gap: 0.4rem; }
label { font-weight: 600; font-size: 0.9rem; }
textarea, input, select {
  padding: 0.6rem 0.8rem;
  border: 1px solid var(--border);
  border-radius: 6px;
  background: var(--entry);
  color: var(--primary);
  font-size: 1rem;
  font-family: inherit;
  width: 100%;
  box-sizing: border-box;
}
textarea { resize: vertical; }
#compteur { font-size: 0.75rem; color: var(--secondary); text-align: right; }
#submitBtn {
  padding: 0.7rem 1.5rem;
  background: var(--primary);
  color: var(--theme);
  border: none;
  border-radius: 6px;
  font-size: 1rem;
  cursor: pointer;
  margin-top: 0.5rem;
}
#submitBtn:disabled { opacity: 0.5; cursor: not-allowed; }
#message { margin-top: 1rem; font-size: 0.9rem; }
.post-footer { display: none; }
</style>

<script>
// Compteur de caractères
const textarea = document.getElementById('anecdote');
const compteur = document.getElementById('compteur');
textarea.addEventListener('input', () => {
  compteur.textContent = `${textarea.value.length}/500`;
});

// Soumission
document.getElementById('anecdoteForm').addEventListener('submit', async (e) => {
  e.preventDefault();
  const btn = document.getElementById('submitBtn');
  const msg = document.getElementById('message');
  
  btn.disabled = true;
  btn.textContent = 'Envoi en cours...';

  try {
    const res = await fetch('https://TON_WORKER.workers.dev/submit', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        anecdote: document.getElementById('anecdote').value,
        secteur:  document.getElementById('secteur').value,
        pseudo:   document.getElementById('pseudo').value || 'Anonyme'
      })
    });

    if (res.ok) {
      msg.style.color = 'green';
      msg.textContent = '✅ Merci ! Ton anecdote sera publiée après modération.';
      e.target.reset();
      compteur.textContent = '0/500';
    } else {
      throw new Error();
    }
  } catch {
    msg.style.color = 'red';
    msg.textContent = '❌ Une erreur est survenue, réessaie dans quelques instants.';
  } finally {
    btn.disabled = false;
    btn.textContent = 'Envoyer mon anecdote';
  }
});
</script>