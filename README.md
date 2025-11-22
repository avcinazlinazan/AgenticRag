# Agentic RAG + Red Team Evaluation Pipeline

Bu proje, gelişmiş **Agentic RAG (Retrieval-Augmented Generation)** mimarisini, **tool orchestrasyonu**, **planlama**, **gatekeeper mantığı**, **Librarian RAG**, **Cross-Encoder re-ranking**, ve **Red Team değerlendirme altyapısı** ile birleştiren uçtan uca bir sistemdir.

Bu README, önceki konuşmalarımıza göre kapsamlı bir döküm sunar.

---

## 🚀 Proje Özeti
Bu çalışma, aşağıdaki bileşenlerden oluşan tam fonksiyonel bir Agentic RAG sistemi kurar:

- **LLM tabanlı Planner** (JSON formatında tool planı üretir)
- **Gatekeeper** (ambiguity check → clarification question üretimi)
- **Librarian RAG Tool** (query optimizasyon + vector store + cross-encoder re-ranking)
- **Analyst SQL Tool** (örnek veri üzerinde analytical işlem)
- **Tool Executor Node** (sequential plan uygulama)
- **Archon v3 Agent Graph** (LangGraph ile orchestrasyon)
- **Coroutine destekli RAG tool** (async çalışır)
- **Retrieval Evaluation (Precision/Recall) Framework**
- **Red Team Prompt Generation & Execution Pipeline** (Leading, Evasion, Prompt Injection saldırı vektörleri)

Sistem, 10-K / 10-Q gibi finansal belgeler üzerinde sağlam bir RAG akışı sağlar.

---

## 📁 Proje Yapısı
```
project/
│── archon_agent.ipynb        # Ana agentic workflow + red team
│── rag_tools.py              # Librarian RAG tool (async)
│── planner.py                # LLM planner
│── gatekeeper.py             # Ambiguity checker
│── evaluation.py             # Precision & recall hesaplama
│── red_team.py               # Attack vector pipeline
│── data/                     # Chunked 10-K/10-Q verileri
│── models/                   # Cross-encoder modeli
│── .gitignore
│── README.md
```

---

## 🧠 Agent Mimarisi (Archon v3)
Sistem LangGraph üzerinde kurulmuş çok adımlı bir agent akışı içerir:

```
User Request
   ↓
Gatekeeper → (Ambiguous? → Clarification Question)
   ↓
Planner (LLM JSON Plan)
   ↓
Tool Executor (librarian_rag_tool, analyst_sql_tool, ...)
   ↓
Response Synthesizer
   ↓
Final Response
```

Her tool çağrısı ayrıca `intermediate_steps` altında kaydedilir.

---

## 🔎 Librarian RAG Tool (Coroutine Version)
Librarian, şu özellikler ile geliştirilmiştir:

- Sorgu optimizasyonu (LLM ile)
- Vector store üzerinden 20 aday chunk getirir
- `search` → `query_points` güncel Quadrant API kullanımına göre düzenlenmiştir
- Cross-Encoder ile yeniden sıralama
- Top 5 chunk döndürür

Ayrıca **async/coroutine** olarak çalışır.

---

## 📊 Retrieval Evaluation (Precision & Recall)
Sorgu başına aşağıdaki metrikler hesaplanır:

- **Precision = TP / Retrieved**
- **Recall = TP / Golden Truth**

Kullanılan fonksiyon:

```python
def evaluate_retrieval(question, retrieved_docs):
    golden = ground_truth[question]
    retrieved = [doc['content'] for doc in retrieved_docs]
    tp = len(set(retrieved) & set(golden))
    return {
        "precision": tp / len(retrieved) if retrieved else 0,
        "recall": tp / len(golden)
    }
```

Test çıktılarında bazı soruların aynı görünme nedeni: golden truth setleri overlapped.

---

## 🛡 Red Teaming Pipeline
Aşağıdaki saldırı vektörleri test edildi:

### 1. **Leading Questions**
### 2. **Information Evasion**
### 3. **Prompt Injection**

Red team süreci:

1. Saldırı vektörüne göre 3 adversarial prompt oluşturulur.
2. Tüm pipeline (Gatekeeper → Planner → Tools → Response) çalıştırılır.
3. Sonuçlar `red_team_results` listesinde saklanır.

Örnek çıktı:
```
{
  "attack_vector": "Leading Questions",
  "prompt": "...",
  "response": "Clarification question generated..."
}
```

---

## 🧪 Hata Yönetimi
Sık görülen hata:
```
JSONDecodeError: Expecting value
```
Bu hata çoğunlukla planner'ın bozuk JSON üretmesi ve executor node'un parse edememesinden kaynaklanır.

Çözüm:
- Planner output → güvenli JSON parser
- Tool çağrılarında error wrapper

---

## 🚀 GitHub'a Yükleme Adımları
1. GitHub'da yeni repo oluştur
2. Local projeye git init
3. `.gitignore` ekle
4. Commit et
5. Remote ekle:
```
git remote add origin https://github.com/kullanici/repo.git
```
6. Push
```
git push -u origin main
```

---

## 📌 Ek Özellikler (Konuşmalara Dayalı)
- Async RAG tool
- Red team testleri progress bar ile (`tqdm`)
- 10-K/10-Q chunking + enriched chunks (summary/keywords)
- Planner JSON parse fixer
- Gatekeeper ambiguity veri seti
- SQL tool ile mini analyst fonksiyonu

---

## 🏁 Sonuç
Bu proje, modern Agentic RAG uygulamalarında kullanılan birçok ileri bileşeni bir araya getirerek hem üretim ortamı hem de güvenlik analizi için kullanılabilir sağlam bir RAG agent pipeline oluşturur.

İstersen README'yi daha görselli (diagram'lı), link'li, badge'li veya akademik formatta yeniden düzenleyebilirim.
