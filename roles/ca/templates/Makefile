all: {{ ca_intermediate_dir }}/certs/{{ common_name }}-chain.crt

# echo rasputin > ca_pass.txt
#
# # root key
# sudo openssl genrsa \
#   -aes256 \
#   -passout file:ca_pass.txt \
#   -out $root_ca/private/ca.key.pem 4096
# # password: rasputin
# sudo chmod 400 $root_ca/private/ca.key.pem


{{ ca_root_dir }}/private/ca.key.pem:
	openssl genrsa \
	  -aes256 \
	  -passout file:{{ ca_pass_file }} \
	  -out {{ ca_root_dir }}/private/ca.key.pem 4096
	chmod 400 {{ ca_root_dir }}/private/ca.key.pem

# # root certificate
# sudo openssl req \
#   -new \
#   -x509 \
#   -days 7300 \
#   -sha256 \
#   -extensions v3_ca \
#   -subj '/O=My Company Name LTD./OU=Root/L=Paradise/ST=CA/C=US/CN=ns1.example.com' \
#   -config openssl.cnf \
#   -passin file:ca_pass.txt \
#   -key $root_ca/private/ca.key.pem \
#   -out $root_ca/certs/ca.cert.pem
# # password: rasputin
# sudo chmod 444 $root_ca/certs/ca.cert.pem

{{ ca_root_dir }}/certs/ca.cert.pem: {{ ca_root_dir }}/private/ca.key.pem
	openssl req \
	  -new \
	  -x509 \
	  -days 7300 \
	  -sha256 \
	  -extensions v3_ca \
	  -subj '/O={{ ca_organization }}/OU={{ ca_organizational_unit }}/L={{ ca_locality }}/ST={{ ca_state_or_province }}/C={{ ca_country }}/CN={{ ca_common_name}}' \
	  -config {{ ca_root_dir }}/openssl.cnf \
	  -passin file:{{ ca_pass_file }} \
	  -key {{ ca_root_dir }}/private/ca.key.pem \
	  -out {{ ca_root_dir }}/certs/ca.cert.pem
	chmod 444 {{ ca_root_dir }}/certs/ca.cert.pem


# # Create the intermediate key
# sudo openssl genrsa \
#   -aes256 \
#   -passout file:ca_pass.txt \
#   -out $root_ca_intermediate/private/intermediate.key.pem 4096
#   # password: rasputin
# sudo chmod 400 $root_ca_intermediate/private/intermediate.key.pem

{{ ca_intermediate_dir }}/private/intermediate.key.pem: {{ ca_root_dir }}/certs/ca.cert.pem
	openssl genrsa \
	  -aes256 \
	  -passout file:{{ ca_pass_file }} \
	  -out {{ ca_intermediate_dir }}/private/intermediate.key.pem 4096
	chmod 400 {{ ca_intermediate_dir }}/private/intermediate.key.pem

# # Create intermediate csr
# sudo openssl req \
#   -new -sha256 \
#   -subj '/O=My Company Name LTD./OU=Intermediate/L=Paradise/ST=CA/C=US/CN=ns1.example.com' \
#   -config $root_ca_intermediate/openssl.cnf \
#   -passin file:ca_pass.txt \
#   -key $root_ca_intermediate/private/intermediate.key.pem \
#   -out $root_ca_intermediate/csr/intermediate.csr.pem

{{ ca_intermediate_dir }}/csr/intermediate.csr.pem: {{ ca_intermediate_dir }}/private/intermediate.key.pem
	openssl req \
	  -new -sha256 \
	  -subj '/O={{ ca_organization }}/OU={{ ca_organizational_unit }}/L={{ ca_locality }}/ST={{ ca_state_or_province }}/C={{ ca_country }}/CN={{ ca_common_name}}' \
	  -config {{ ca_intermediate_dir }}/openssl.cnf \
	  -passin file:{{ ca_pass_file }} \
	  -key {{ ca_intermediate_dir }}/private/intermediate.key.pem \
	  -out {{ ca_intermediate_dir }}/csr/intermediate.csr.pem

# # Create the intermediate certificate
# sudo openssl ca \
#   -config $root_ca/openssl.cnf \
#   -extensions v3_intermediate_ca \
#   -days 3650 \
#   -notext \
#   -md sha256 \
#   -passin file:ca_pass.txt \
#   -batch \
#   -in $root_ca_intermediate/csr/intermediate.csr.pem \
#   -out $root_ca_intermediate/certs/intermediate.cert.pem
{{ ca_intermediate_dir }}/certs/intermediate.cert.pem: {{ ca_intermediate_dir }}/csr/intermediate.csr.pem
	openssl ca \
	  -config {{ ca_root_dir }}/openssl.cnf \
	  -extensions v3_intermediate_ca \
	  -days 3650 \
	  -notext \
	  -md sha256 \
	  -passin file:{{ ca_pass_file }} \
	  -batch \
	  -in {{ ca_intermediate_dir }}/csr/intermediate.csr.pem \
	  -out {{ ca_intermediate_dir }}/certs/intermediate.cert.pem

# sudo openssl genrsa \
#   -out $root_ca_intermediate/private/raspberry3-wifi.example.com.key.pem \
#   2048
# sudo chmod 400 $root_ca_intermediate/private/raspberry3-wifi.example.com.key.pem
{{ ca_intermediate_dir }}/private/{{ common_name }}.key.pem: {{ ca_intermediate_dir }}/certs/intermediate.cert.pem
	openssl genrsa \
	  -out {{ ca_intermediate_dir }}/private/{{ common_name }}.key.pem \
	  2048

# # create csr
# sudo openssl req \
#   -new \
#   -sha256 \
#   -subj '/O=My Company Name LTD./OU=Docker Registry/L=Paradise/ST=CA/C=US/CN=raspberry3-wifi.example.com' \
#   -config $root_ca_intermediate/openssl.cnf \
#   -key $root_ca_intermediate/private/raspberry3-wifi.example.com.key.pem \
#   -out $root_ca_intermediate/csr/raspberry3-wifi.example.com.csr.pem
{{ ca_intermediate_dir }}/csr/{{ common_name }}.csr.pem: {{ ca_intermediate_dir }}/private/{{ common_name }}.key.pem
	openssl req \
	  -new \
	  -sha256 \
	  -subj '/O={{ organization }}/OU={{ organizational_unit }}/L={{ locality }}/ST={{ state_or_province }}/C={{ country }}/CN={{ common_name}}' \
	  -config {{ ca_intermediate_dir }}/openssl.cnf \
	  -key {{ ca_intermediate_dir }}/private/{{ common_name }}.key.pem \
	  -out {{ ca_intermediate_dir }}/csr/{{ common_name }}.csr.pem


# # create cert
# # server_cert for server cert, usr_cert for user cert
# sudo openssl ca \
#   -config $root_ca_intermediate/openssl.cnf \
#   -extensions server_cert \
#   -md sha256 \
#   -days 375 \
#   -notext \
#   -passin file:ca_pass.txt \
#   -batch \
#   -in $root_ca_intermediate/csr/raspberry3-wifi.example.com.csr.pem \
#   -out $root_ca_intermediate/certs/raspberry3-wifi.example.com.cert.pem
# sudo chmod 444 $root_ca_intermediate/certs/raspberry3-wifi.example.com.cert.pem

{{ ca_intermediate_dir }}/certs/{{ common_name }}.cert.pem: {{ ca_intermediate_dir }}/csr/{{ common_name }}.csr.pem
	openssl ca \
	  -config {{ ca_intermediate_dir }}/openssl.cnf \
	  -extensions server_cert \
	  -md sha256 \
	  -days 375 \
	  -notext \
	  -passin file:{{ ca_pass_file }} \
	  -batch \
	  -in {{ ca_intermediate_dir }}/csr/{{ common_name }}.csr.pem \
	  -out {{ ca_intermediate_dir }}/certs/{{ common_name }}.cert.pem
	chmod 444 {{ ca_intermediate_dir }}/certs/{{ common_name }}.cert.pem


# sudo cat \
#   $root_ca_intermediate/certs/raspberry3-wifi.example.com.cert.pem \
#   $root_ca_intermediate/certs/ca-chain.cert.pem \
#   > ~/certs/raspberry3-wifi.example.com.crt

{{ ca_intermediate_dir }}/certs/{{ common_name }}-chain.crt: {{ ca_intermediate_dir }}/certs/{{ common_name }}.cert.pem {{ ca_intermediate_dir }}/certs/intermediate.cert.pem {{ ca_root_dir }}/certs/ca.cert.pem
	cat \
	  {{ ca_intermediate_dir }}/certs/{{ common_name }}.cert.pem \
	  {{ ca_root_dir }}/certs/ca.cert.pem \
	  {{ ca_intermediate_dir }}/certs/intermediate.cert.pem \
	  > {{ ca_intermediate_dir }}/certs/{{ common_name }}-chain.crt
