# TechChallenge-2

Roteiro de Testes

1. Realizar abertura dos 5 serviços, apresentando o repositório e o Dockerfile, na ordem:

ls -la /fiap/auth-service
ls -la /fiap/flag-service
ls -la /fiap/targeting-service
ls -la /fiap/evaluation-service
ls -la /fiap/analytics-service

dockecat /fiap/flag-service/requirements.txt

2. Mostrar o arquivo cd /fiap e vim docker-compose.yml

3. Executar a subida do docker-compose up -d

4. Executar o comando docker containers ls para mostrar os containers em funcionamento
    
5. Realizar criação das chaves, utilizando o script ./fiap/.keygen.sh

  O script executa as seguintes chamadas:
   curl -X POST http://localhost:8001/admin/keys \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer admin-secreto-123" \
    -d '{"name": "auth-service-key"}'

  curl -X POST http://localhost:8001/admin/keys \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer admin-secreto-123" \
    -d '{"name": "flag-service-key"}'

  curl -X POST http://localhost:8001/admin/keys \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer admin-secreto-123" \
    -d '{"name": "targeting-service-key"}'

  curl -X POST http://localhost:8001/admin/keys \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer admin-secreto-123" \
    -d '{"name": "evaluation-service-key"}'

  curl -X POST http://localhost:8001/admin/keys \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer admin-secreto-123" \
    -d '{"name": "analytics-service-key"}' 

6. Realizar a criação de uma flag com:
  curl -X POST http://localhost:8002/flags \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer tm_key_8f5baeb09a9ad04e2443fece8d624cd1a46231d131023b58c0c401906a565dfe" \
    -d '{
        "name": "enable-new-dashboard",
        "description": "Ativa o novo dashboard para usuarios",
        "is_enabled": true
    }'

7. Realizar a criação de uma regra de targeting:
  curl -X POST http://localhost:8003/rules \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer tm_key_f5383cc66835189b3a9bbadff86fc8e1f771392a49e2a596989145ef7fe7685c" \
    -d '{
        "flag_name": "enable-new-dashboard",
        "is_enabled": true,
        "rules": {
            "type": "PERCENTAGE",
            "value": 50
        }
    }'

8. Buscar a regra criada
   curl http://localhost:8003/rules/enable-new-dashboard \
    -H "Authorization: Bearer tm_key_a67b165e84f0e8226f58d1409e6d8c655a378c02e51c087696facb472ec42a5c"

9. Realizar a atualização da chave do evaluation-service, dentro de vim /fiap/evaluation-service/.env

10. Reinicializar o serviço do evaluation-service com:
    docker rm -f evaluation-service
    docker run -d --name evaluation-service --network tc-network --env-file ./evaluation-service/.env -p 8004:8004 fiap-evaluation-service:latest
    
12. Testar o serviço de avaliação
    curl "http://localhost:8004/evaluate?user_id=user-123&flag_name=enable-new-dashboard"

13. Verificar o arquivo de logs com: docker logs evaluation-service

14. Realizar a abertura do console AWS e mostrar que o SQS recebeu as mensagens

15. Gerar novas avaliações
    curl "http://localhost:8004/evaluate?user_id=test-user-1&flag_name=enable-new-dashboard"
    curl "http://localhost:8004/evaluate?user_id=test-user-2&flag_name=enable-new-dashboard"

16. Realizar a abertura dos logs de docker logs analytics-service

17. Realizar a abertura do console AWS e mostrar que o DynamoDB recebeu os itens na tabela
