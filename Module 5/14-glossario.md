# 📚 Termos-chave deste Módulo

> 🔤 Em ordem alfabética.

### 📦 Accelerator

Uma solução funcionando empacotada para que o próximo engagement a **configure**, em vez de reconstruí-la. Partes específicas de cliente são expostas como parâmetros documentados, as suposições são escritas, e um eval é empacotado para provar que o ativo ainda funciona num novo contexto.

### ✅ Contribution readiness

O que um maintainer precisa para verificar uma contribuição: código focado, um exemplo executável, um teste que prova o comportamento, uma declaração de suposições de ambiente, e direitos confirmados de contribuir o código.

### 🚀 Deployment platform

Onde um workload Claude roda. As seis opções: a first-party Claude API, Claude Platform on AWS, Claude in Amazon Bedrock, Claude on Amazon Bedrock (legacy), Google Vertex AI, e plataformas third-party. <mark>O mesmo modelo pode diferir por plataforma em identidade, residência de dados, latência e custo.</mark>

### 🔖 Model alias versus pinned ID

Um alias como `opus` ou `sonnet` resolve para uma versão recomendada que se atualiza com o tempo e pode diferir por plataforma. Um **ID completo fixado** é um snapshot fixo. <mark>Fixar é o que impede uma mudança upstream de modelo de virar uma mudança silenciosa em produção.</mark>

### 🔒 Trust boundary

O seam onde dado ou instruções se movem de um ambiente de deployment para outro numa aplicação multi-componente. Conteúdo buscado por um componente é não-confiável quando chega ao próximo, então o componente receptor o trata como **dado**, não instruções.
