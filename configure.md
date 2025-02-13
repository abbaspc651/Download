#include <iostream>
#include <string>
#include <curl/curl.h> // For HTTP requests (requires libcurl)

size_t WriteCallback(void* contents, size_t size, size_t nmemb, std::string* output) {
    size_t totalSize = size * nmemb;
    if (output) {
        output->append((char*)contents, totalSize);
    }
    return totalSize;
}

void fetchGitHubRepo(const std::string& repoOwner, const std::string& repoName, const std::string& authToken) {
    CURL* curl;
    CURLcode res;
    std::string response;

    curl_global_init(CURL_GLOBAL_DEFAULT);
    curl = curl_easy_init();

    if (curl) {
        std::string url = "https://api.github.com/repos/" + repoOwner + "/" + repoName;

        // Set the URL
        curl_easy_setopt(curl, CURLOPT_URL, url.c_str());

        // Add authentication header
        struct curl_slist* headers = nullptr;
        headers = curl_slist_append(headers, ("Authorization: token " + authToken).c_str());
        headers = curl_slist_append(headers, "User-Agent: CppGitHubClient");

        curl_easy_setopt(curl, CURLOPT_HTTPHEADER, headers);

        // Set the callback for response data
        curl_easy_setopt(curl, CURLOPT_WRITEFUNCTION, WriteCallback);
        curl_easy_setopt(curl, CURLOPT_WRITEDATA, &response);

        // Perform the request
        res = curl_easy_perfor
