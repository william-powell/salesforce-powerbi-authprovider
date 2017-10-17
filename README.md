# salesforce-powerbi-authprovider
Intended to use with PowerBI-Javascript and "Application owns the data" scenario

https://github.com/Microsoft/PowerBI-JavaScript

Used to maintain an access token for generating asset specific access tokens.
https://msdn.microsoft.com/en-US/library/mt784614.aspx

Once this auth provider is in place, a new instance can be created (and authorized) and can then be used as a Named Credential.

Example function that gets access token for a tile asset, using the Named Credential:
```
@AuraEnabled
public static PowerBIResponse getAccessTokenForTile(string groupId, string dashboardId, string tileId) {
    HttpRequest req = new HttpRequest();
    req.setEndpoint('callout:PowerBI/' + groupId + '/dashboards/' + dashboardId + '/tiles/' + tileId + '/GenerateToken');
    req.setMethod('POST');

    String postString = '{"accessLevel": "View"}';
    req.setHeader('Content-Length', string.valueOf(postString.length()));
    req.setBody(postString);
    Http http = new Http();
    HTTPResponse res = http.send(req);

    if (res.getStatusCode() != 200) {
        PowerBIResponse pbiResponse = new PowerBIResponse();
        pbiResponse.error = string.valueOf(res.getStatusCode());
        pbiResponse.error_description = res.getStatus();
        return pbiResponse;
    }

    return getPowerBiResponse(res.getBody());
}

private static PowerBIResponse getPowerBiResponse(String response) {
    PowerBIResponse parsedResponse = (PowerBIResponse) System.JSON.deserialize(response, PowerBIResponse.class);        
    return parsedResponse;
} 

public class PowerBIResponse {
    @AuraEnabled
    public String token;
    @AuraEnabled
    public String error;
    @AuraEnabled
    public String error_description;
}
```
