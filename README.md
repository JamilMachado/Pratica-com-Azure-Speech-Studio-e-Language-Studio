# Pratica-com-Azure-Speech-Studio-e-Language-Studio
Prática com Azure Speech Studio e Language Studio
1. Áudio e Reconhecimento de Fala (Speech to Text)
Copiei o arquivo de áudio conforme indicado no Lab/09-speech do Microsoft Learn.
Utilizei um áudio exemplo (.wav) com gravação de uma opinião sobre um produto.

Criei o serviço de fala no Azure:

Nome: SpeechAIService

Região: eastus

Recurso: Speech

Nível de preço: Gratuito (F0)

Copiei a chave e o endpoint para um arquivo .env:

env
Copiar
Editar
SPEECH_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
SPEECH_ENDPOINT=https://eastus.api.cognitive.microsoft.com/
Código Python para transcrição (Speech SDK):

python
Copiar
Editar
import os
import azure.cognitiveservices.speech as speechsdk
from dotenv import load_dotenv

load_dotenv()

speech_key = os.getenv("SPEECH_KEY")
service_region = "eastus"

speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
audio_input = speechsdk.AudioConfig(filename="review_audio.wav")
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_input)

result = speech_recognizer.recognize_once()
print("Transcrição:", result.text)
📌 Insight: A precisão da transcrição foi alta, especialmente com arquivos bem gravados. O Speech Studio permite testes visuais e ajustes de pronúncia se necessário.

2. Análise de Linguagem Natural
Salvei os exemplos de reviews (disponíveis no Lab/06-text-analysis do Microsoft Learn) em arquivos de texto:

review1.txt

review2.txt

review3.txt

Criei um serviço Language no Azure:

Nome: LanguageAIService

Região: brazilsouth

Recurso: Language

Nível de preço: Gratuito (F0)

Copiei a chave e endpoint para o arquivo .env:

env
Copiar
Editar
LANGUAGE_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
LANGUAGE_ENDPOINT=https://brazilsouth.api.cognitive.microsoft.com/
3. Função para análise de sentimentos
Exemplo de código usando Azure Text Analytics v3.2:

python
Copiar
Editar
import requests

key = os.getenv("LANGUAGE_KEY")
endpoint = os.getenv("LANGUAGE_ENDPOINT")

headers = {
    "Ocp-Apim-Subscription-Key": key,
    "Content-Type": "application/json",
}

documents = {
    "documents": [
        {"id": "1", "language": "en", "text": open("review1.txt").read()},
        {"id": "2", "language": "en", "text": open("review2.txt").read()},
        {"id": "3", "language": "en", "text": open("review3.txt").read()}
    ]
}

response = requests.post(
    f"{endpoint}/text/analytics/v3.2/sentiment",
    headers=headers,
    json=documents
)

results = response.json()
for doc in results["documents"]:
    print(f"Review {doc['id']} - Sentimento: {doc['sentiment']}, Confiança: {doc['confidenceScores']}")
✅ Resultados Obtidos
Review	Sentimento	Score Confiança
1	positive	{'positive': 0.94, 'neutral': 0.05, 'negative': 0.01}
2	negative	{'positive': 0.02, 'neutral': 0.10, 'negative': 0.88}
3	neutral	{'positive': 0.40, 'neutral': 0.50, 'negative': 0.10}

📌 Insight: A API detecta com precisão polaridades sutis, e é possível segmentar a análise por sentença para diagnósticos mais granulares.

4. Encerramento da Prática
Após as análises, os serviços foram excluídos para evitar cobranças adicionais.

SpeechAIService: excluído com sucesso.

LanguageAIService: excluído com sucesso.

🧠 Aprendizados e Recomendações
Integração simples: as bibliotecas da Azure facilitam o uso direto via Python com retorno estruturado (JSON).

Foco em segurança: guardar chaves em .env é fundamental.

Boas práticas: sempre excluir os serviços quando terminar a prática para evitar custos indevidos.

Expansões possíveis: incluir análise de linguagem com entidade nomeada, detecção de idioma, e análise por frases.
