# Claude Flux Pipeline

텍스트를 입력하면 Claude API가 프롬프트를 최적화하고 FLUX.1 모델로 이미지를 생성하는 파이프라인

## 아키텍처

```
사용자 텍스트 입력 (한국어/영어)
        ↓
Claude API - 프롬프트 최적화 + 영어 변환
        ↓
HuggingFace FLUX.1-schnell - 이미지 생성
        ↓
이미지 반환
```

## 사용 기술

| 기술 | 역할 |
|------|------|
| Claude API (claude-sonnet-4-6) | 자연어를 FLUX 최적화 영어 프롬프트로 변환 |
| FLUX.1-schnell | HuggingFace Inference API로 이미지 생성 |
| FastAPI | REST API 서버 |
| RunPod | GPU 서버 (RTX 2000 Ada, VRAM 16GB) |

## API 엔드포인트

| 메서드 | 경로 | 설명 |
|--------|------|------|
| GET | `/` | 서버 상태 확인 |
| POST | `/generate` | 텍스트 → 이미지 생성 |
| GET | `/image/{filename}` | 생성된 이미지 조회 |
| GET | `/docs` | Swagger UI |

## 요청 / 응답 예시

```bash
curl -X POST http://localhost:8000/generate \
  -H "Content-Type: application/json" \
  -d '{"text": "석양이 지는 바닷가"}'
```

```json
{
  "original_input": "석양이 지는 바닷가",
  "optimized_prompt": "golden sunset over ocean beach, warm orange and pink sky, gentle waves, cinematic lighting, 8k photorealistic",
  "image_filename": "abc123.png"
}
```

## 실행 방법

```bash
cp .env.example .env
# .env 파일에 API 키 입력

pip install -r requirements.txt
cd app && uvicorn main:app --host 0.0.0.0 --port 8000
```

## 환경 변수

```
ANTHROPIC_API_KEY=   # Anthropic Claude API 키
HF_TOKEN=            # HuggingFace API 토큰
```

## 디렉토리 구조

```
├── app/
│   ├── main.py              # FastAPI 서버 + 엔드포인트
│   ├── prompt_optimizer.py  # Claude API 프롬프트 최적화
│   └── image_generator.py   # HuggingFace FLUX API 호출
├── outputs/                 # 생성된 이미지 저장
├── requirements.txt
└── .env.example
```
