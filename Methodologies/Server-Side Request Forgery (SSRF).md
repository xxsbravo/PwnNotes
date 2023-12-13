As the name implies, **Server Side Request Forgery** (*SSRF*) is a web security vulnerability that occurs when an attacker can manipulate the requests sent by a web application from the server side. In an *SSRF* attack, the attacker typically leverages the ability to make requests to internal resources or external third-party services, exploiting the trust placed in the server to access sensitive information or conduct various malicious activities.

Key characteristics of *SSRF* attacks include:

1. **Request Manipulation:** The attacker tricks the server into making unintended HTTP requests to internal resources or external services by manipulating input parameters that specify the target URL.
    
2. **Internal Network Access:** *SSRF* can be used to make requests to internal resources, such as databases, APIs, or local files, that are not intended to be directly accessible from the external network.
    
3. **External Service Abuse:** Attackers may abuse *SSRF* to make requests to external services on the internet, potentially causing service disruption, using the server as a proxy, or extracting information from third-party services.
    
4. **Exploitation of Trust:** *SSRF* exploits the trust that a server has in its ability to make legitimate requests. If not properly validated and protected, SSRF can lead to unauthorized access and data exposure.
    
5. **Security Bypass:** *SSRF* can be used to bypass firewalls or security measures by making requests to internal resources that would otherwise be protected.
    

Common vectors for *SSRF* include input parameters that allow specifying URLs, such as image URLs, file uploads, or API endpoints. To prevent *SSRF* vulnerabilities, developers should validate and sanitize user-input that includes URLs, and servers should be configured to restrict access to sensitive internal resources. Additionally, using whitelists of allowed URLs or IP addresses can help mitigate the risk of *SSRF* attacks.

#SSRF

---