# Generate Money Guard Application Script

## Overview

The `generate-money-guard-application` script is a utility tool that creates new applications with public/private key pairs for the Money Guard system. This script generates the necessary cryptographic keys and application configuration required for secure communication between Money Guard components.

## Purpose

This script is used to:
- Generate a new application with a unique ID
- Create RSA public/private key pairs for secure communication
- Output application credentials for environment configuration
- Enable secure authentication between Money Guard services

## Usage

### Command

```bash
npm run generate-money-guard-application
```

Or using the direct TypeScript execution:

```bash
ts-node -r tsconfig-paths/register src/shared/scripts/generate-money-guard-application.ts
```

### Prerequisites

- Node.js (20 or higher)
- TypeScript and ts-node installed
- Money Guard Blockchain Core project setup
- Required dependencies installed (`npm install`)

## Output

The script generates and displays the following information:

### Application Details
- **Application ID**: A unique UUID identifier for the application
- **Application Name**: "MoneyGuard" (default)
- **Description**: Application description (optional)
- **Logo URL**: Application logo URL (optional)

### Cryptographic Keys
- **Private Key**: RSA private key in PEM format for signing requests
- **Public Key**: RSA public key in PEM format for signature verification

### Example Output

```json
Application created: {
  "id": "1f6a667b-b674-4acf-b00f-3fd66eddc411",
  "name": "MoneyGuard",
  "description": null,
  "logoUrl": null,
  "privateKey": "-----BEGIN PRIVATE KEY-----\nMIIEvQIBADANBgkqhkiG9w0BAQEFAASCBKcwggSjAgEAAoIBAQC5ozQhIxP8Iprt\n/eRAPbesAT75Dfr/rPs6CNsO7f/kzt5wDRvdyKUWIHpM0VA3AP2upa9kUT4Dcq1a\nbjj2e153kWOaihlqNv5NCd1/ebB3uHLbHzpEswsNa76eE6xdFxAOQ6o9HzymPkcd\nEdpjJVlnU1VQPRECk93pAOvX/FghobP48HhSy7AtEMqcIO5cEONXozYBSwpSPWNM\nZdu9IP8Q3jCqO+N/WU4ETlvT4R+zIhq3kZ4Lpn1mumkBh8BIXu24h+RYodxFF26b\nHb98Xt1suIIMhnQ6N97wQYxZ/2G7BqfRnKZlN7dauAwfJfURkNyxZTRjH2fHg6GR\nwhJzqcHPAgMBAAECggEANOjON8+ep5NZYxGlF6Y/jlfAkg2pB2xPImhZvzSYZaeM\ngJVL5veEDf1tvi0+Vp8Crt9sUQRSz3rE6+xUcfQRpbSblfVcbwiDjIOOTQHrCQRa\nQBfzbJc3RDj8WGO1Ecc7/UTN5aPcjmlbrW5kNzPy+l8nBrqQ+ZIZgoqPrbyvhc3r\nZXmwsCaF4msfvPuNLAO7DjbikZ0G2iX7opRVRbkLrIH/Q6lTgvWn0J5a4oOR4bS3\nU8gXu5M8P1a/yvlnPeQaiYyem+B3YYtZBcOINqrRMGveN0NrJobMg6d3IiuJCBV4\ndiyBLW+hmN9NGvWzZ/JnKUWxIkmVDD7WNZkZ5TbQkQKBgQDlnJVwA5TFwLth+lvR\nevNSHbElCouDYFDPtvDC6VoWqYc87vE+wtRUOhsnjNkgsmhYx5RvL0EWpOza1Jdb\n9QDLjAGkGSRvyz/nGeduFrkYFFGkDAt3X6/auFGqgXYMVHiSy8V+MzkGerQXJ5g3\njBnKIDrH9U5ZLqVZESpzpK7FdwKBgQDO+Nq8TWqOecA9JM085pXQ5s96LA2nfhb7\nw2XU1bgBOt7lusM/Rc1st9isnHaiDNi3nFAUNXP/+UEdttafg7cMEnf3y5x2aX7L\nPAVEalyZwmk3PB3sa/xRiYZJTESZXrx0AUNsa7XR201gSgHKi9AyN4WVzisVA3ga\nv5BEgBxcaQKBgHjAyo5oM7t0SpeSdOtL/971oo3aZ9iltJoomaHH2wsECQ1wlArv\np37MOC+qkR3y9ZGLFHMMpOxlu/KxzxAbX1msvcgxOMpaV7cQFnf9F0akmPLvKrCS\n8AbU3I4RG6YMXoTFCY3/HkRcD44F0rWTxpRYMtGqHpQkRez7bBn5R7itAoGBAIbw\naGvsbVSGvHcgz06tZ2/Ti+GzZxOUeZebRYEF9fmXUEunADT4ZX5EPtgLCq2Mgp7z\nIqKKVx7Ee1fMJRHK5kTQpbbA4TmyfiAy+ZwpXEZDIiYI49mIbfEbIJoZR4BW67cW\nO8csUlaIDGKC3bgYf5y95jJNQHlq0Zte2chPXi5BAoGAaotTNHrWLdwpM58LEuw/\n1jkNvFAS5nDuScx+g6B66M1H0cjWOrhdvzRvmo1MT5ef8VP0dU5lFKmNHOGTonEB\nDSTnZwTeURhi944cFTw2NOFH6R+nPHeIvY2BqKqV+nmtPJKHrRstyEbQTmjXaU7c\nPSvlOLJr9b2p4qM0iTWs1mw=\n-----END PRIVATE KEY-----\n",
  "publicKey": "-----BEGIN PUBLIC KEY-----\nMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAuaM0ISMT/CKa7f3kQD23\nrAE++Q36/6z7OgjbDu3/5M7ecA0b3cilFiB6TNFQNwD9rqWvZFE+A3KtWm449nte\nd5FjmooZajb+TQndf3mwd7hy2x86RLMLDWu+nhOsXRcQDkOqPR88pj5HHRHaYyVZ\nZ1NVUD0RApPd6QDr1/xYIaGz+PB4UsuwLRDKnCDuXBDjV6M2AUsKUj1jTGXbvSD/\nEN4wqjvjf1lOBE5b0+EfsyIat5GeC6Z9ZrppAYfASF7tuIfkWKHcRRdumx2/fF7d\nbLiCDIZ0Ojfe8EGMWf9huwan0ZymZTe3WrgMHyX1EZDcsWU0Yx9nx4OhkcISc6nB\nzwIDAQAB\n-----END PUBLIC KEY-----\n"
}

Application ID: 1f6a667b-b674-4acf-b00f-3fd66eddc411

Please update the .env file with the following values:
MONEY_GUARD_APPLICATION_ID=1f6a667b-b674-4acf-b00f-3fd66eddc411
```

## Environment Configuration

After running the script, you need to update your `.env` file with the generated application ID:

```bash
# Add this to your .env file
MONEY_GUARD_APPLICATION_ID=1f6a667b-b674-4acf-b00f-3fd66eddc411
```

### Additional Environment Variables

Depending on your setup, you may also need to configure:

```bash
# For Money Guard Backend integration
BLOCKCHAIN_APPLICATION_ID=1f6a667b-b674-4acf-b00f-3fd66eddc411
BLOCKCHAIN_APPLICATION_SECRET_KEY=<private_key_from_output>
BLOCKCHAIN_APPLICATION_PUBLIC_KEY=<public_key_from_output>
BLOCKCHAIN_WEBHOOK_URL=http://your-webhook-url.com

# For Money Guard Blockchain Core
MONEY_GUARD_APPLICATION_ID=1f6a667b-b674-4acf-b00f-3fd66eddc411
MONEY_GUARD_WEBHOOK_URL=http://your-webhook-url.com
```

## Security Considerations

### Private Key Security
- **Never commit private keys to version control**
- Store private keys securely in environment variables or secure key management systems
- Rotate keys regularly for enhanced security
- Use different key pairs for different environments (development, staging, production)

### Key Management Best Practices
- Keep private keys confidential and accessible only to authorized personnel
- Use secure channels when sharing keys between team members
- Implement proper access controls for key storage systems
- Monitor key usage and implement logging for security auditing

## Integration with Money Guard Components

### Money Guard Backend
The generated application credentials are used by the Money Guard Backend to:
- Authenticate with the Money Guard Blockchain Core service
- Sign webhook requests for secure communication
- Verify incoming webhook signatures

### Money Guard Blockchain Core
The application ID is used to:
- Identify the calling application
- Validate webhook signatures
- Manage application-specific configurations and permissions

## Troubleshooting

### Common Issues

1. **Script not found**
   - Ensure you're in the correct project directory (Money Guard Blockchain Core)
   - Verify that the script exists at `src/shared/scripts/generate-money-guard-application.ts`

2. **TypeScript compilation errors**
   - Run `npm install` to ensure all dependencies are installed
   - Check that TypeScript and ts-node are properly configured

3. **Permission errors**
   - Ensure you have proper file system permissions
   - Check that the output directory is writable

### Verification

To verify the generated keys work correctly:
1. Test the application ID in your environment configuration
2. Verify webhook communication between services
3. Check application logs for authentication errors

## Related Documentation

- [Money Guard Blockchain Core](money-guard-blockchain-core.md)
- [Money Guard Backend](money-guard-backend.md)
- [User Acceptance Testing (UAT)](user-acceptance-testing-uat.md)

## Script Location

The script is located at:
```
src/shared/scripts/generate-money-guard-application.ts
```

In the Money Guard Blockchain Core project repository.
