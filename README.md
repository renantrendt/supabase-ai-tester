# Supabase AI RLS Tests Generator

An AI-powered tool that automatically generates and runs comprehensive test cases for your Supabase Row Level Security (RLS) policies. Using Claude AI, it analyzes your policies and creates test scenarios to verify their effectiveness.

## Features

- 🤖 AI-powered test case generation
- 🔒 Comprehensive RLS policy testing
- 📊 Detailed test reports
- 🚀 Easy setup and configuration
- 💾 Automatic test case storage
- 📝 Human-readable results

## Prerequisites

Before using this package, you need to:

1. Have a Supabase project with RLS policies you want to test
2. Install the required database function by running this SQL in your Supabase SQL editor:

\`\`\`sql
CREATE OR REPLACE FUNCTION public.get_policies(target_table text)
RETURNS TABLE (
    table_name text,
    policy_name text,
    definition text,
    command text,
    permissive text
)
LANGUAGE SQL
SECURITY DEFINER
AS $$
    SELECT
        schemaname || '.' || tablename as table_name,
        policyname as policy_name,
        regexp_replace(regexp_replace(coalesce(qual, ''), '\n', ' ', 'g'), '\s+', ' ', 'g') as definition,
        cmd as command,
        permissive
    FROM pg_policies
    WHERE (schemaname || '.' || tablename) = target_table
    OR tablename = target_table;
$$;
\`\`\`

## Installation

```bash
npm install supabase-ai-rls-tests-generator
```

## Quick Start

1. Run the setup wizard:
```bash
npx setup-tests
```

2. Enter your credentials when prompted:
- Supabase URL
- Supabase service role key
- Claude API key

3. Run the tests:
```bash
npx test-rls
```

## Configuration

The package uses a separate \`.env.rls-test\` file to store its configuration, ensuring it doesn't interfere with your project's existing \`.env\` file. The setup wizard will create this file for you with the following variables:

```env
SUPABASE_RLS_URL=your_supabase_url
SUPABASE_RLS_KEY=your_supabase_key
SUPABASE_RLS_CLAUDE_KEY=your_claude_key
```

This file is automatically added to \`.gitignore\` to prevent accidentally committing sensitive information.

## Test Results

Test results are stored in the \`generated\` folder:
- \`generated/tests\`: Contains the generated test cases
- \`generated/results\`: Contains the test execution results

Each test run creates timestamped files so you can track changes over time.

## Example Test Output

```json
{
  "timestamp": "2024-02-25T14-30-45-789Z",
  "total": 10,
  "passed": 8,
  "failed": 2,
  "details": [
    {
      "test": {
        "description": "User can read their own posts",
        "method": "select",
        "path": "posts",
        "expectedStatus": 200
      },
      "success": true,
      "actual": 200,
      "expected": 200
    }
    // ... more test results
  ]
}
```

## API Usage

You can also use the package programmatically:

```typescript
import { SupabaseAITester } from 'supabase-ai-rls-tests-generator';

const tester = new SupabaseAITester({
  supabaseUrl: process.env.SUPABASE_RLS_URL,
  supabaseKey: process.env.SUPABASE_RLS_KEY,
  claudeKey: process.env.SUPABASE_RLS_CLAUDE_KEY,
  config: {
    verbose: true
  }
});

async function runTests() {
  try {
    const results = await tester.runRLSTests('your_table_name');
    console.log('Test Results:', results);
  } catch (error) {
    console.error('Test Error:', error);
  }
}
```

## Contributing

We welcome contributions! Please see our [Contributing Guidelines](CONTRIBUTING.md) for details.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Support

If you encounter any issues or have questions:

1. Check the [Issues](https://github.com/renantrendt/supabase-ai-rls-tests-generator/issues) page
2. Open a new issue if needed
3. Join the discussion in existing issues

## Authors

- **Renan Serrano** - *Initial work* - [renantrendt](https://github.com/renantrendt)

## Acknowledgments

- Thanks to [Supabase](https://supabase.io/) for their amazing platform
- Thanks to [Anthropic](https://www.anthropic.com/) for Claude AI
- Thanks to all contributors who help improve this project